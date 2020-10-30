---
title: 'Конфигурация :::no-loc(SignalR)::: ASP.NET Core'
author: bradygaster
description: 'Узнайте, как настроить ASP.NET Core :::no-loc(SignalR)::: приложения.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061240"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="27623-103">Конфигурация :::no-loc(SignalR)::: ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27623-103">ASP.NET Core :::no-loc(SignalR)::: configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27623-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27623-105">ASP.NET Core :::no-loc(SignalR)::: поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27623-105">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27623-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="27623-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27623-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27623-108">`AddJsonProtocol`можно добавить после [добавления :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-108">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27623-109">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="27623-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27623-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="27623-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27623-111">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27623-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27623-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27623-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="27623-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27623-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27623-114">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="27623-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27623-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27623-116">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="27623-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27623-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27623-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27623-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-118">MessagePack serialization options</span></span>

<span data-ttu-id="27623-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27623-120">Дополнительные сведения см. [в разделе MessagePack в :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-120">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27623-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="27623-122">Configure server options</span></span>

<span data-ttu-id="27623-123">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-123">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="27623-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-124">Option</span></span> | <span data-ttu-id="27623-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-125">Default Value</span></span> | <span data-ttu-id="27623-126">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27623-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-127">30 seconds</span></span> | <span data-ttu-id="27623-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="27623-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27623-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="27623-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27623-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27623-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-131">15 seconds</span></span> | <span data-ttu-id="27623-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="27623-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27623-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-134">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-134">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-135">15 seconds</span></span> | <span data-ttu-id="27623-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="27623-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27623-137">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="27623-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27623-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27623-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="27623-139">All installed protocols</span></span> | <span data-ttu-id="27623-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="27623-140">Protocols supported by this hub.</span></span> <span data-ttu-id="27623-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27623-142">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27623-143">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="27623-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27623-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27623-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="27623-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27623-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-146">32 KB</span></span> | <span data-ttu-id="27623-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="27623-148">1</span><span class="sxs-lookup"><span data-stu-id="27623-148">1</span></span> | <span data-ttu-id="27623-149">Максимальное число методов концентратора, которые каждый клиент может вызывать параллельно перед постановкой в очередь.</span><span class="sxs-lookup"><span data-stu-id="27623-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="27623-150">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `Add:::no-loc(SignalR):::` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-150">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="27623-151">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `Add:::no-loc(SignalR):::` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27623-151">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27623-152">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="27623-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27623-153">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27623-154">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27623-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27623-155">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27623-155">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="27623-156">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-156">Option</span></span> | <span data-ttu-id="27623-157">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-157">Default Value</span></span> | <span data-ttu-id="27623-158">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27623-159">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-159">32 KB</span></span> | <span data-ttu-id="27623-160">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="27623-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27623-161">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27623-162">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="27623-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27623-163">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="27623-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27623-164">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-164">32 KB</span></span> | <span data-ttu-id="27623-165">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="27623-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27623-166">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27623-167">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-167">All Transports are enabled.</span></span> | <span data-ttu-id="27623-168">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="27623-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27623-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-169">See below.</span></span> | <span data-ttu-id="27623-170">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="27623-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27623-171">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-171">See below.</span></span> | <span data-ttu-id="27623-172">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="27623-173">0</span><span class="sxs-lookup"><span data-stu-id="27623-173">0</span></span> | <span data-ttu-id="27623-174">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="27623-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="27623-175">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="27623-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="27623-176">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27623-177">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-177">Option</span></span> | <span data-ttu-id="27623-178">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-178">Default Value</span></span> | <span data-ttu-id="27623-179">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27623-180">90 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-180">90 seconds</span></span> | <span data-ttu-id="27623-181">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="27623-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27623-182">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="27623-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27623-183">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27623-184">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-184">Option</span></span> | <span data-ttu-id="27623-185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-185">Default Value</span></span> | <span data-ttu-id="27623-186">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27623-187">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-187">5 seconds</span></span> | <span data-ttu-id="27623-188">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="27623-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27623-189">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="27623-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27623-190">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="27623-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27623-191">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="27623-191">Configure client options</span></span>

<span data-ttu-id="27623-192">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27623-193">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="27623-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27623-194">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="27623-194">Configure logging</span></span>

<span data-ttu-id="27623-195">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="27623-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27623-196">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="27623-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27623-197">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27623-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-198">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="27623-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27623-199">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="27623-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27623-200">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="27623-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27623-201">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="27623-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27623-202">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="27623-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27623-203">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27623-204">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="27623-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27623-205">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27623-206">Это полезно при настройке :::no-loc(SignalR)::: ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="27623-206">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27623-207">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-207">The following table lists the available log levels.</span></span> <span data-ttu-id="27623-208">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27623-209">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни** , будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="27623-210">Строка</span><span class="sxs-lookup"><span data-stu-id="27623-210">String</span></span>                      | <span data-ttu-id="27623-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27623-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27623-212">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="27623-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27623-213">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="27623-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27623-214">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="27623-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27623-215">Дополнительные сведения о ведении журналов см. в [ :::no-loc(SignalR)::: документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27623-215">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27623-216">:::no-loc(SignalR):::Клиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-216">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27623-217">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27623-218">В следующем фрагменте кода показано, как использовать `java.util.logging` с :::no-loc(SignalR)::: клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="27623-218">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27623-219">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="27623-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27623-220">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="27623-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27623-221">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="27623-221">Configure allowed transports</span></span>

<span data-ttu-id="27623-222">Транспорты, используемые, :::no-loc(SignalR)::: можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-222">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27623-223">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="27623-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27623-224">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-224">All transports are enabled by default.</span></span>

<span data-ttu-id="27623-225">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="27623-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27623-226">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27623-227">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="27623-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27623-228">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27623-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27623-229">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27623-230">:::no-loc(SignalR):::Клиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="27623-230">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27623-231">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="27623-231">Configure bearer authentication</span></span>

<span data-ttu-id="27623-232">Чтобы предоставить данные проверки подлинности вместе с :::no-loc(SignalR)::: запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="27623-232">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27623-233">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27623-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27623-234">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="27623-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27623-235">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27623-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27623-236">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27623-237">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27623-238">В :::no-loc(SignalR)::: клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27623-238">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27623-239">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27623-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27623-240">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27623-241">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="27623-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27623-242">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="27623-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-243">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-244">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-244">Option</span></span> | <span data-ttu-id="27623-245">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-245">Default value</span></span> | <span data-ttu-id="27623-246">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27623-247">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-248">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-248">Timeout for server activity.</span></span> <span data-ttu-id="27623-249">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-250">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-251">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27623-252">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-252">15 seconds</span></span> | <span data-ttu-id="27623-253">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-254">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-255">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-256">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-256">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-257">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-257">15 seconds</span></span> | <span data-ttu-id="27623-258">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-259">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-260">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27623-261">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="27623-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27623-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-263">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-263">Option</span></span> | <span data-ttu-id="27623-264">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-264">Default value</span></span> | <span data-ttu-id="27623-265">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27623-266">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-267">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-267">Timeout for server activity.</span></span> <span data-ttu-id="27623-268">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27623-269">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-270">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27623-271">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-272">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-273">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-274">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-275">Java</span><span class="sxs-lookup"><span data-stu-id="27623-275">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-276">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-276">Option</span></span> | <span data-ttu-id="27623-277">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-277">Default value</span></span> | <span data-ttu-id="27623-278">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27623-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27623-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27623-280">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-281">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-281">Timeout for server activity.</span></span> <span data-ttu-id="27623-282">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-283">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-284">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27623-285">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-285">15 seconds</span></span> | <span data-ttu-id="27623-286">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-287">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-288">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-289">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-289">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27623-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27623-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27623-291">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-292">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-293">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-294">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27623-295">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="27623-295">Configure additional options</span></span>

<span data-ttu-id="27623-296">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="27623-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-297">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-298">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="27623-298">.NET Option</span></span> |  <span data-ttu-id="27623-299">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-299">Default value</span></span> | <span data-ttu-id="27623-300">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27623-301">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27623-302">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-303">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-304">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-304">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27623-305">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-305">Empty</span></span> | <span data-ttu-id="27623-306">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="27623-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="27623-307">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-307">Empty</span></span> | <span data-ttu-id="27623-308">Коллекция HTTP :::no-loc(cookie)::: s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-308">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27623-309">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-309">Empty</span></span> | <span data-ttu-id="27623-310">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27623-311">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-311">5 seconds</span></span> | <span data-ttu-id="27623-312">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-312">WebSockets only.</span></span> <span data-ttu-id="27623-313">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="27623-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27623-314">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="27623-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27623-315">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-315">Empty</span></span> | <span data-ttu-id="27623-316">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27623-317">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27623-318">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="27623-319">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="27623-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27623-320">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="27623-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27623-321">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как :::no-loc(Cookie)::: s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="27623-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27623-322">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27623-323">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27623-324">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="27623-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27623-325">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27623-326">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="27623-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27623-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-328">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-328">JavaScript Option</span></span> | <span data-ttu-id="27623-329">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-329">Default Value</span></span> | <span data-ttu-id="27623-330">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27623-331">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27623-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="27623-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="27623-333">Словарь заголовков, отправленных с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="27623-334">Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока.</span><span class="sxs-lookup"><span data-stu-id="27623-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27623-335">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="27623-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27623-336">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-337">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-338">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-338">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="27623-339">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="27623-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="27623-340">Служба приложений Azure использует :::no-loc(cookie)::: для прикрепленных сеансов s и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="27623-340">Azure App Service uses :::no-loc(cookie):::s for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="27623-341">Дополнительные сведения о CORS с :::no-loc(SignalR)::: см. в разделе <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="27623-341">For more info on CORS with :::no-loc(SignalR):::, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-342">Java</span><span class="sxs-lookup"><span data-stu-id="27623-342">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-343">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="27623-343">Java Option</span></span> | <span data-ttu-id="27623-344">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-344">Default Value</span></span> | <span data-ttu-id="27623-345">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27623-346">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27623-347">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-348">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-349">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-349">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="27623-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27623-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27623-351">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-351">Empty</span></span> | <span data-ttu-id="27623-352">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27623-353">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27623-354">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="27623-355">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27623-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27623-356">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="27623-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27623-357">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27623-358">ASP.NET Core :::no-loc(SignalR)::: поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27623-358">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27623-359">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="27623-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27623-360">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27623-361">`AddJsonProtocol`можно добавить после [добавления :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-361">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27623-362">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="27623-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27623-363">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="27623-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27623-364">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27623-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27623-365">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27623-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="27623-366">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27623-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27623-367">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="27623-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27623-368">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27623-369">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="27623-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27623-370">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27623-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27623-371">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-371">MessagePack serialization options</span></span>

<span data-ttu-id="27623-372">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27623-373">Дополнительные сведения см. [в разделе MessagePack в :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-373">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-374">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27623-375">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="27623-375">Configure server options</span></span>

<span data-ttu-id="27623-376">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-376">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="27623-377">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-377">Option</span></span> | <span data-ttu-id="27623-378">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-378">Default Value</span></span> | <span data-ttu-id="27623-379">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27623-380">30 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-380">30 seconds</span></span> | <span data-ttu-id="27623-381">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="27623-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27623-382">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="27623-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27623-383">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27623-384">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-384">15 seconds</span></span> | <span data-ttu-id="27623-385">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="27623-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27623-386">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-387">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-387">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-388">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-388">15 seconds</span></span> | <span data-ttu-id="27623-389">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="27623-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27623-390">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="27623-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27623-391">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27623-392">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="27623-392">All installed protocols</span></span> | <span data-ttu-id="27623-393">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="27623-393">Protocols supported by this hub.</span></span> <span data-ttu-id="27623-394">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27623-395">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27623-396">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="27623-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27623-397">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27623-398">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="27623-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27623-399">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-399">32 KB</span></span> | <span data-ttu-id="27623-400">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="27623-401">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `Add:::no-loc(SignalR):::` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-401">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="27623-402">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `Add:::no-loc(SignalR):::` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27623-402">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27623-403">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="27623-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27623-404">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27623-405">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27623-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27623-406">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27623-406">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="27623-407">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-407">Option</span></span> | <span data-ttu-id="27623-408">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-408">Default Value</span></span> | <span data-ttu-id="27623-409">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27623-410">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-410">32 KB</span></span> | <span data-ttu-id="27623-411">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="27623-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27623-412">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27623-413">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="27623-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27623-414">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="27623-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27623-415">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-415">32 KB</span></span> | <span data-ttu-id="27623-416">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="27623-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27623-417">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27623-418">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-418">All Transports are enabled.</span></span> | <span data-ttu-id="27623-419">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="27623-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27623-420">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-420">See below.</span></span> | <span data-ttu-id="27623-421">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="27623-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27623-422">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-422">See below.</span></span> | <span data-ttu-id="27623-423">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="27623-424">0</span><span class="sxs-lookup"><span data-stu-id="27623-424">0</span></span> | <span data-ttu-id="27623-425">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="27623-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="27623-426">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="27623-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="27623-427">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27623-428">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-428">Option</span></span> | <span data-ttu-id="27623-429">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-429">Default Value</span></span> | <span data-ttu-id="27623-430">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27623-431">90 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-431">90 seconds</span></span> | <span data-ttu-id="27623-432">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="27623-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27623-433">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="27623-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27623-434">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27623-435">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-435">Option</span></span> | <span data-ttu-id="27623-436">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-436">Default Value</span></span> | <span data-ttu-id="27623-437">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27623-438">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-438">5 seconds</span></span> | <span data-ttu-id="27623-439">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="27623-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27623-440">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="27623-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27623-441">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="27623-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27623-442">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="27623-442">Configure client options</span></span>

<span data-ttu-id="27623-443">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27623-444">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="27623-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27623-445">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="27623-445">Configure logging</span></span>

<span data-ttu-id="27623-446">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="27623-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27623-447">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="27623-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27623-448">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27623-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-449">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="27623-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27623-450">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="27623-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27623-451">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="27623-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27623-452">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="27623-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27623-453">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="27623-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27623-454">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27623-455">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="27623-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27623-456">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27623-457">Это полезно при настройке :::no-loc(SignalR)::: ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="27623-457">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27623-458">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-458">The following table lists the available log levels.</span></span> <span data-ttu-id="27623-459">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27623-460">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни** , будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="27623-461">Строка</span><span class="sxs-lookup"><span data-stu-id="27623-461">String</span></span>                      | <span data-ttu-id="27623-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27623-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27623-463">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="27623-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27623-464">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="27623-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27623-465">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="27623-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27623-466">Дополнительные сведения о ведении журналов см. в [ :::no-loc(SignalR)::: документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27623-466">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27623-467">:::no-loc(SignalR):::Клиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-467">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27623-468">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27623-469">В следующем фрагменте кода показано, как использовать `java.util.logging` с :::no-loc(SignalR)::: клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="27623-469">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27623-470">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="27623-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27623-471">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="27623-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27623-472">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="27623-472">Configure allowed transports</span></span>

<span data-ttu-id="27623-473">Транспорты, используемые, :::no-loc(SignalR)::: можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-473">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27623-474">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="27623-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27623-475">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-475">All transports are enabled by default.</span></span>

<span data-ttu-id="27623-476">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="27623-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27623-477">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27623-478">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="27623-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27623-479">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27623-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27623-480">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27623-481">:::no-loc(SignalR):::Клиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="27623-481">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27623-482">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="27623-482">Configure bearer authentication</span></span>

<span data-ttu-id="27623-483">Чтобы предоставить данные проверки подлинности вместе с :::no-loc(SignalR)::: запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="27623-483">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27623-484">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27623-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27623-485">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="27623-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27623-486">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27623-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27623-487">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27623-488">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27623-489">В :::no-loc(SignalR)::: клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27623-489">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27623-490">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27623-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27623-491">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27623-492">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="27623-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27623-493">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="27623-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-494">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-495">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-495">Option</span></span> | <span data-ttu-id="27623-496">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-496">Default value</span></span> | <span data-ttu-id="27623-497">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27623-498">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-499">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-499">Timeout for server activity.</span></span> <span data-ttu-id="27623-500">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-501">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-502">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27623-503">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-503">15 seconds</span></span> | <span data-ttu-id="27623-504">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-505">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-506">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-507">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-507">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-508">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-508">15 seconds</span></span> | <span data-ttu-id="27623-509">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-510">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-511">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27623-512">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="27623-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27623-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-514">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-514">Option</span></span> | <span data-ttu-id="27623-515">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-515">Default value</span></span> | <span data-ttu-id="27623-516">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27623-517">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-518">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-518">Timeout for server activity.</span></span> <span data-ttu-id="27623-519">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27623-520">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-521">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27623-522">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-523">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-524">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-525">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-526">Java</span><span class="sxs-lookup"><span data-stu-id="27623-526">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-527">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-527">Option</span></span> | <span data-ttu-id="27623-528">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-528">Default value</span></span> | <span data-ttu-id="27623-529">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27623-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27623-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27623-531">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-532">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-532">Timeout for server activity.</span></span> <span data-ttu-id="27623-533">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-534">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-535">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27623-536">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-536">15 seconds</span></span> | <span data-ttu-id="27623-537">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-538">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-539">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-540">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-540">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27623-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27623-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27623-542">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-543">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-544">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-545">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27623-546">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="27623-546">Configure additional options</span></span>

<span data-ttu-id="27623-547">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="27623-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-548">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-549">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="27623-549">.NET Option</span></span> |  <span data-ttu-id="27623-550">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-550">Default value</span></span> | <span data-ttu-id="27623-551">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27623-552">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27623-553">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-554">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-555">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-555">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27623-556">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-556">Empty</span></span> | <span data-ttu-id="27623-557">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="27623-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="27623-558">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-558">Empty</span></span> | <span data-ttu-id="27623-559">Коллекция HTTP :::no-loc(cookie)::: s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-559">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27623-560">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-560">Empty</span></span> | <span data-ttu-id="27623-561">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27623-562">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-562">5 seconds</span></span> | <span data-ttu-id="27623-563">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-563">WebSockets only.</span></span> <span data-ttu-id="27623-564">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="27623-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27623-565">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="27623-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27623-566">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-566">Empty</span></span> | <span data-ttu-id="27623-567">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27623-568">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27623-569">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="27623-570">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="27623-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27623-571">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="27623-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27623-572">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как :::no-loc(Cookie)::: s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="27623-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27623-573">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27623-574">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27623-575">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="27623-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27623-576">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27623-577">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="27623-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27623-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-579">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-579">JavaScript Option</span></span> | <span data-ttu-id="27623-580">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-580">Default Value</span></span> | <span data-ttu-id="27623-581">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27623-582">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27623-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="27623-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27623-584">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="27623-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27623-585">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-586">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-587">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-587">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-588">Java</span><span class="sxs-lookup"><span data-stu-id="27623-588">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-589">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="27623-589">Java Option</span></span> | <span data-ttu-id="27623-590">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-590">Default Value</span></span> | <span data-ttu-id="27623-591">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27623-592">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27623-593">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-594">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-595">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-595">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="27623-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27623-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27623-597">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-597">Empty</span></span> | <span data-ttu-id="27623-598">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27623-599">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27623-600">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27623-601">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27623-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27623-602">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="27623-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27623-603">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27623-604">ASP.NET Core :::no-loc(SignalR)::: поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27623-604">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27623-605">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="27623-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27623-606">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="27623-607">`AddJsonProtocol`можно добавить после [добавления :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-607">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="27623-608">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="27623-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27623-609">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="27623-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27623-610">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="27623-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="27623-611">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27623-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="27623-612">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27623-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27623-613">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="27623-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27623-614">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="27623-615">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="27623-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="27623-616">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="27623-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27623-617">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-617">MessagePack serialization options</span></span>

<span data-ttu-id="27623-618">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27623-619">Дополнительные сведения см. [в разделе MessagePack в :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-619">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-620">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27623-621">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="27623-621">Configure server options</span></span>

<span data-ttu-id="27623-622">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-622">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="27623-623">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-623">Option</span></span> | <span data-ttu-id="27623-624">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-624">Default Value</span></span> | <span data-ttu-id="27623-625">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27623-626">30 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-626">30 seconds</span></span> | <span data-ttu-id="27623-627">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="27623-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27623-628">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="27623-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27623-629">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27623-630">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-630">15 seconds</span></span> | <span data-ttu-id="27623-631">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="27623-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27623-632">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-633">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-633">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-634">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-634">15 seconds</span></span> | <span data-ttu-id="27623-635">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="27623-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27623-636">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="27623-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27623-637">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27623-638">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="27623-638">All installed protocols</span></span> | <span data-ttu-id="27623-639">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="27623-639">Protocols supported by this hub.</span></span> <span data-ttu-id="27623-640">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27623-641">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27623-642">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="27623-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="27623-643">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="27623-644">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="27623-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="27623-645">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-645">32 KB</span></span> | <span data-ttu-id="27623-646">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="27623-647">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `Add:::no-loc(SignalR):::` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-647">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="27623-648">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `Add:::no-loc(SignalR):::` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27623-648">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27623-649">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="27623-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27623-650">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27623-651">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27623-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="27623-652">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27623-652">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="27623-653">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-653">Option</span></span> | <span data-ttu-id="27623-654">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-654">Default Value</span></span> | <span data-ttu-id="27623-655">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27623-656">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-656">32 KB</span></span> | <span data-ttu-id="27623-657">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="27623-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="27623-658">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27623-659">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="27623-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27623-660">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="27623-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27623-661">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-661">32 KB</span></span> | <span data-ttu-id="27623-662">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="27623-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="27623-663">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27623-664">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-664">All Transports are enabled.</span></span> | <span data-ttu-id="27623-665">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="27623-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27623-666">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-666">See below.</span></span> | <span data-ttu-id="27623-667">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="27623-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27623-668">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-668">See below.</span></span> | <span data-ttu-id="27623-669">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27623-670">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27623-671">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-671">Option</span></span> | <span data-ttu-id="27623-672">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-672">Default Value</span></span> | <span data-ttu-id="27623-673">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27623-674">90 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-674">90 seconds</span></span> | <span data-ttu-id="27623-675">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="27623-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27623-676">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="27623-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27623-677">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27623-678">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-678">Option</span></span> | <span data-ttu-id="27623-679">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-679">Default Value</span></span> | <span data-ttu-id="27623-680">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27623-681">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-681">5 seconds</span></span> | <span data-ttu-id="27623-682">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="27623-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27623-683">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="27623-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27623-684">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="27623-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27623-685">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="27623-685">Configure client options</span></span>

<span data-ttu-id="27623-686">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27623-687">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="27623-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27623-688">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="27623-688">Configure logging</span></span>

<span data-ttu-id="27623-689">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="27623-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27623-690">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="27623-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27623-691">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27623-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-692">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="27623-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27623-693">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="27623-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27623-694">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="27623-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27623-695">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="27623-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27623-696">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="27623-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27623-697">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27623-698">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="27623-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="27623-699">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="27623-700">Это полезно при настройке :::no-loc(SignalR)::: ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="27623-700">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="27623-701">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-701">The following table lists the available log levels.</span></span> <span data-ttu-id="27623-702">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="27623-703">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни** , будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="27623-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="27623-704">Строка</span><span class="sxs-lookup"><span data-stu-id="27623-704">String</span></span>                      | <span data-ttu-id="27623-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="27623-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="27623-706">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="27623-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="27623-707">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="27623-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="27623-708">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="27623-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27623-709">Дополнительные сведения о ведении журналов см. в [ :::no-loc(SignalR)::: документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27623-709">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27623-710">:::no-loc(SignalR):::Клиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-710">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27623-711">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27623-712">В следующем фрагменте кода показано, как использовать `java.util.logging` с :::no-loc(SignalR)::: клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="27623-712">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27623-713">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="27623-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27623-714">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="27623-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27623-715">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="27623-715">Configure allowed transports</span></span>

<span data-ttu-id="27623-716">Транспорты, используемые, :::no-loc(SignalR)::: можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-716">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27623-717">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="27623-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27623-718">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-718">All transports are enabled by default.</span></span>

<span data-ttu-id="27623-719">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="27623-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27623-720">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27623-721">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="27623-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="27623-722">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="27623-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="27623-723">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27623-724">:::no-loc(SignalR):::Клиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="27623-724">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27623-725">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="27623-725">Configure bearer authentication</span></span>

<span data-ttu-id="27623-726">Чтобы предоставить данные проверки подлинности вместе с :::no-loc(SignalR)::: запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="27623-726">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27623-727">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27623-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27623-728">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="27623-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27623-729">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27623-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27623-730">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27623-731">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27623-732">В :::no-loc(SignalR)::: клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27623-732">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27623-733">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27623-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27623-734">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27623-735">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="27623-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27623-736">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="27623-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-737">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-738">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-738">Option</span></span> | <span data-ttu-id="27623-739">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-739">Default value</span></span> | <span data-ttu-id="27623-740">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27623-741">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-742">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-742">Timeout for server activity.</span></span> <span data-ttu-id="27623-743">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-744">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-745">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27623-746">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-746">15 seconds</span></span> | <span data-ttu-id="27623-747">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-748">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-749">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-750">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-750">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-751">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-751">15 seconds</span></span> | <span data-ttu-id="27623-752">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-753">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-754">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27623-755">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="27623-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27623-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-757">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-757">Option</span></span> | <span data-ttu-id="27623-758">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-758">Default value</span></span> | <span data-ttu-id="27623-759">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27623-760">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-761">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-761">Timeout for server activity.</span></span> <span data-ttu-id="27623-762">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27623-763">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-764">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27623-765">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-766">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-767">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-768">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-769">Java</span><span class="sxs-lookup"><span data-stu-id="27623-769">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-770">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-770">Option</span></span> | <span data-ttu-id="27623-771">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-771">Default value</span></span> | <span data-ttu-id="27623-772">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27623-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27623-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27623-774">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-775">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-775">Timeout for server activity.</span></span> <span data-ttu-id="27623-776">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-777">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-778">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27623-779">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-779">15 seconds</span></span> | <span data-ttu-id="27623-780">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-781">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-782">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-783">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-783">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27623-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27623-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27623-785">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-786">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-787">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-788">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27623-789">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="27623-789">Configure additional options</span></span>

<span data-ttu-id="27623-790">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="27623-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-791">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-792">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="27623-792">.NET Option</span></span> |  <span data-ttu-id="27623-793">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-793">Default value</span></span> | <span data-ttu-id="27623-794">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27623-795">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27623-796">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-797">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-798">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-798">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27623-799">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-799">Empty</span></span> | <span data-ttu-id="27623-800">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="27623-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="27623-801">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-801">Empty</span></span> | <span data-ttu-id="27623-802">Коллекция HTTP :::no-loc(cookie)::: s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-802">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27623-803">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-803">Empty</span></span> | <span data-ttu-id="27623-804">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27623-805">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-805">5 seconds</span></span> | <span data-ttu-id="27623-806">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-806">WebSockets only.</span></span> <span data-ttu-id="27623-807">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="27623-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27623-808">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="27623-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27623-809">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-809">Empty</span></span> | <span data-ttu-id="27623-810">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27623-811">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27623-812">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="27623-813">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="27623-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27623-814">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="27623-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27623-815">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как :::no-loc(Cookie)::: s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="27623-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27623-816">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27623-817">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27623-818">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="27623-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27623-819">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27623-820">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="27623-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27623-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-822">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-822">JavaScript Option</span></span> | <span data-ttu-id="27623-823">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-823">Default Value</span></span> | <span data-ttu-id="27623-824">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27623-825">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27623-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="27623-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27623-827">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="27623-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27623-828">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-829">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-830">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-830">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-831">Java</span><span class="sxs-lookup"><span data-stu-id="27623-831">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-832">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="27623-832">Java Option</span></span> | <span data-ttu-id="27623-833">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-833">Default Value</span></span> | <span data-ttu-id="27623-834">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27623-835">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27623-836">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-837">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-838">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-838">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="27623-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27623-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27623-840">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-840">Empty</span></span> | <span data-ttu-id="27623-841">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27623-842">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27623-843">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27623-844">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27623-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27623-845">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="27623-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27623-846">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27623-847">ASP.NET Core :::no-loc(SignalR)::: поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27623-847">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27623-848">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="27623-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27623-849">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="27623-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="27623-850">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="27623-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27623-851">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="27623-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27623-852">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="27623-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="27623-853">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27623-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="27623-854">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27623-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27623-855">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="27623-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27623-856">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27623-857">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-857">MessagePack serialization options</span></span>

<span data-ttu-id="27623-858">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27623-859">Дополнительные сведения см. [в разделе MessagePack в :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-859">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-860">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27623-861">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="27623-861">Configure server options</span></span>

<span data-ttu-id="27623-862">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-862">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="27623-863">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-863">Option</span></span> | <span data-ttu-id="27623-864">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-864">Default Value</span></span> | <span data-ttu-id="27623-865">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="27623-866">30 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-866">30 seconds</span></span> | <span data-ttu-id="27623-867">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="27623-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="27623-868">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="27623-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="27623-869">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="27623-870">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-870">15 seconds</span></span> | <span data-ttu-id="27623-871">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="27623-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27623-872">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-873">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-873">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-874">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-874">15 seconds</span></span> | <span data-ttu-id="27623-875">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="27623-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27623-876">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="27623-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27623-877">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27623-878">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="27623-878">All installed protocols</span></span> | <span data-ttu-id="27623-879">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="27623-879">Protocols supported by this hub.</span></span> <span data-ttu-id="27623-880">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27623-881">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27623-882">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="27623-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="27623-883">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `Add:::no-loc(SignalR):::` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-883">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="27623-884">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `Add:::no-loc(SignalR):::` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27623-884">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27623-885">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="27623-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27623-886">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27623-887">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27623-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="27623-888">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27623-888">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="27623-889">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-889">Option</span></span> | <span data-ttu-id="27623-890">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-890">Default Value</span></span> | <span data-ttu-id="27623-891">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27623-892">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-892">32 KB</span></span> | <span data-ttu-id="27623-893">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="27623-894">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27623-895">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="27623-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27623-896">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="27623-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27623-897">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-897">32 KB</span></span> | <span data-ttu-id="27623-898">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="27623-899">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27623-900">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-900">All Transports are enabled.</span></span> | <span data-ttu-id="27623-901">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="27623-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27623-902">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-902">See below.</span></span> | <span data-ttu-id="27623-903">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="27623-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27623-904">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-904">See below.</span></span> | <span data-ttu-id="27623-905">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27623-906">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27623-907">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-907">Option</span></span> | <span data-ttu-id="27623-908">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-908">Default Value</span></span> | <span data-ttu-id="27623-909">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27623-910">90 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-910">90 seconds</span></span> | <span data-ttu-id="27623-911">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="27623-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27623-912">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="27623-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27623-913">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27623-914">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-914">Option</span></span> | <span data-ttu-id="27623-915">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-915">Default Value</span></span> | <span data-ttu-id="27623-916">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27623-917">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-917">5 seconds</span></span> | <span data-ttu-id="27623-918">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="27623-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27623-919">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="27623-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27623-920">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="27623-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27623-921">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="27623-921">Configure client options</span></span>

<span data-ttu-id="27623-922">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27623-923">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="27623-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27623-924">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="27623-924">Configure logging</span></span>

<span data-ttu-id="27623-925">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="27623-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27623-926">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="27623-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27623-927">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27623-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-928">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="27623-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27623-929">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="27623-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27623-930">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="27623-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27623-931">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="27623-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27623-932">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="27623-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27623-933">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27623-934">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="27623-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27623-935">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="27623-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27623-936">Дополнительные сведения о ведении журналов см. в [ :::no-loc(SignalR)::: документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27623-936">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27623-937">:::no-loc(SignalR):::Клиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-937">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27623-938">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27623-939">В следующем фрагменте кода показано, как использовать `java.util.logging` с :::no-loc(SignalR)::: клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="27623-939">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27623-940">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="27623-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27623-941">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="27623-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27623-942">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="27623-942">Configure allowed transports</span></span>

<span data-ttu-id="27623-943">Транспорты, используемые, :::no-loc(SignalR)::: можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-943">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27623-944">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="27623-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27623-945">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-945">All transports are enabled by default.</span></span>

<span data-ttu-id="27623-946">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="27623-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27623-947">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="27623-948">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="27623-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27623-949">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="27623-949">Configure bearer authentication</span></span>

<span data-ttu-id="27623-950">Чтобы предоставить данные проверки подлинности вместе с :::no-loc(SignalR)::: запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="27623-950">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27623-951">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27623-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27623-952">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="27623-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27623-953">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27623-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27623-954">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27623-955">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27623-956">В :::no-loc(SignalR)::: клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27623-956">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27623-957">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27623-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27623-958">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27623-959">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="27623-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27623-960">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="27623-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-961">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-962">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-962">Option</span></span> | <span data-ttu-id="27623-963">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-963">Default value</span></span> | <span data-ttu-id="27623-964">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27623-965">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-966">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-966">Timeout for server activity.</span></span> <span data-ttu-id="27623-967">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-968">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-969">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27623-970">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-970">15 seconds</span></span> | <span data-ttu-id="27623-971">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-972">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-973">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-974">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-974">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-975">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-975">15 seconds</span></span> | <span data-ttu-id="27623-976">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-977">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-978">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="27623-979">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="27623-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27623-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-981">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-981">Option</span></span> | <span data-ttu-id="27623-982">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-982">Default value</span></span> | <span data-ttu-id="27623-983">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27623-984">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-985">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-985">Timeout for server activity.</span></span> <span data-ttu-id="27623-986">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27623-987">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-988">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="27623-989">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-990">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-991">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-992">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-993">Java</span><span class="sxs-lookup"><span data-stu-id="27623-993">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-994">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-994">Option</span></span> | <span data-ttu-id="27623-995">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-995">Default value</span></span> | <span data-ttu-id="27623-996">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27623-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27623-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27623-998">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-999">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-999">Timeout for server activity.</span></span> <span data-ttu-id="27623-1000">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-1001">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-1002">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27623-1003">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1003">15 seconds</span></span> | <span data-ttu-id="27623-1004">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-1005">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-1006">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-1007">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-1007">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="27623-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="27623-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="27623-1009">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="27623-1010">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="27623-1011">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="27623-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="27623-1012">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="27623-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27623-1013">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="27623-1013">Configure additional options</span></span>

<span data-ttu-id="27623-1014">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="27623-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-1016">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="27623-1016">.NET Option</span></span> |  <span data-ttu-id="27623-1017">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1017">Default value</span></span> | <span data-ttu-id="27623-1018">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27623-1019">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27623-1020">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1021">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1022">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1022">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27623-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1023">Empty</span></span> | <span data-ttu-id="27623-1024">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="27623-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="27623-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1025">Empty</span></span> | <span data-ttu-id="27623-1026">Коллекция HTTP :::no-loc(cookie)::: s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1026">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27623-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1027">Empty</span></span> | <span data-ttu-id="27623-1028">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27623-1029">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-1029">5 seconds</span></span> | <span data-ttu-id="27623-1030">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-1030">WebSockets only.</span></span> <span data-ttu-id="27623-1031">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="27623-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27623-1032">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="27623-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27623-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1033">Empty</span></span> | <span data-ttu-id="27623-1034">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27623-1035">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27623-1036">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="27623-1037">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="27623-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27623-1038">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="27623-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27623-1039">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как :::no-loc(Cookie)::: s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="27623-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27623-1040">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27623-1041">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27623-1042">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="27623-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27623-1043">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27623-1044">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="27623-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27623-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-1046">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-1046">JavaScript Option</span></span> | <span data-ttu-id="27623-1047">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1047">Default Value</span></span> | <span data-ttu-id="27623-1048">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27623-1049">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27623-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="27623-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27623-1051">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="27623-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27623-1052">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1053">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1054">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1054">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-1055">Java</span><span class="sxs-lookup"><span data-stu-id="27623-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-1056">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="27623-1056">Java Option</span></span> | <span data-ttu-id="27623-1057">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1057">Default Value</span></span> | <span data-ttu-id="27623-1058">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27623-1059">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27623-1060">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1061">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1062">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1062">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="27623-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27623-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27623-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1064">Empty</span></span> | <span data-ttu-id="27623-1065">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27623-1066">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27623-1067">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27623-1068">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27623-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27623-1069">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="27623-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="27623-1070">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="27623-1071">ASP.NET Core :::no-loc(SignalR)::: поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="27623-1071">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="27623-1072">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="27623-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="27623-1073">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="27623-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="27623-1074">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="27623-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="27623-1075">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="27623-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="27623-1076">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="27623-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="27623-1077">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="27623-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="27623-1078">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="27623-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="27623-1079">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="27623-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="27623-1080">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="27623-1081">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="27623-1081">MessagePack serialization options</span></span>

<span data-ttu-id="27623-1082">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="27623-1083">Дополнительные сведения см. [в разделе MessagePack в :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="27623-1083">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-1084">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="27623-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="27623-1085">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="27623-1085">Configure server options</span></span>

<span data-ttu-id="27623-1086">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-1086">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="27623-1087">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1087">Option</span></span> | <span data-ttu-id="27623-1088">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1088">Default Value</span></span> | <span data-ttu-id="27623-1089">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="27623-1090">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1090">15 seconds</span></span> | <span data-ttu-id="27623-1091">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="27623-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="27623-1092">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-1093">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-1093">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="27623-1094">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1094">15 seconds</span></span> | <span data-ttu-id="27623-1095">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="27623-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="27623-1096">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="27623-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="27623-1097">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="27623-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="27623-1098">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="27623-1098">All installed protocols</span></span> | <span data-ttu-id="27623-1099">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="27623-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="27623-1100">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="27623-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="27623-1101">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="27623-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="27623-1102">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="27623-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="27623-1103">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `Add:::no-loc(SignalR):::` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="27623-1103">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="27623-1104">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `Add:::no-loc(SignalR):::` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="27623-1104">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="27623-1105">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="27623-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="27623-1106">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="27623-1107">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="27623-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="27623-1108">В следующей таблице описаны параметры для настройки :::no-loc(SignalR)::: расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27623-1108">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="27623-1109">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1109">Option</span></span> | <span data-ttu-id="27623-1110">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1110">Default Value</span></span> | <span data-ttu-id="27623-1111">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="27623-1112">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-1112">32 KB</span></span> | <span data-ttu-id="27623-1113">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="27623-1114">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="27623-1115">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="27623-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="27623-1116">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="27623-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="27623-1117">32 КБ</span><span class="sxs-lookup"><span data-stu-id="27623-1117">32 KB</span></span> | <span data-ttu-id="27623-1118">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="27623-1119">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="27623-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="27623-1120">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-1120">All Transports are enabled.</span></span> | <span data-ttu-id="27623-1121">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="27623-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="27623-1122">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-1122">See below.</span></span> | <span data-ttu-id="27623-1123">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="27623-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="27623-1124">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="27623-1124">See below.</span></span> | <span data-ttu-id="27623-1125">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="27623-1126">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="27623-1127">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1127">Option</span></span> | <span data-ttu-id="27623-1128">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1128">Default Value</span></span> | <span data-ttu-id="27623-1129">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="27623-1130">90 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1130">90 seconds</span></span> | <span data-ttu-id="27623-1131">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="27623-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="27623-1132">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="27623-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="27623-1133">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="27623-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="27623-1134">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1134">Option</span></span> | <span data-ttu-id="27623-1135">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1135">Default Value</span></span> | <span data-ttu-id="27623-1136">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="27623-1137">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-1137">5 seconds</span></span> | <span data-ttu-id="27623-1138">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="27623-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="27623-1139">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="27623-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="27623-1140">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="27623-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="27623-1141">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="27623-1141">Configure client options</span></span>

<span data-ttu-id="27623-1142">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="27623-1143">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="27623-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="27623-1144">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="27623-1144">Configure logging</span></span>

<span data-ttu-id="27623-1145">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="27623-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="27623-1146">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="27623-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="27623-1147">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="27623-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="27623-1148">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="27623-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="27623-1149">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="27623-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="27623-1150">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="27623-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="27623-1151">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="27623-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="27623-1152">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="27623-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="27623-1153">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="27623-1154">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="27623-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="27623-1155">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="27623-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="27623-1156">Дополнительные сведения о ведении журналов см. в [ :::no-loc(SignalR)::: документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="27623-1156">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="27623-1157">:::no-loc(SignalR):::Клиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-1157">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="27623-1158">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="27623-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="27623-1159">В следующем фрагменте кода показано, как использовать `java.util.logging` с :::no-loc(SignalR)::: клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="27623-1159">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="27623-1160">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="27623-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="27623-1161">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="27623-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="27623-1162">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="27623-1162">Configure allowed transports</span></span>

<span data-ttu-id="27623-1163">Транспорты, используемые, :::no-loc(SignalR)::: можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-1163">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="27623-1164">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="27623-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="27623-1165">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="27623-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="27623-1166">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="27623-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="27623-1167">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="27623-1168">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="27623-1168">Configure bearer authentication</span></span>

<span data-ttu-id="27623-1169">Чтобы предоставить данные проверки подлинности вместе с :::no-loc(SignalR)::: запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="27623-1169">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="27623-1170">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="27623-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="27623-1171">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="27623-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="27623-1172">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="27623-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="27623-1173">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="27623-1174">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="27623-1175">В :::no-loc(SignalR)::: клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="27623-1175">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="27623-1176">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="27623-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="27623-1177">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="27623-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="27623-1178">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="27623-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="27623-1179">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="27623-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-1181">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1181">Option</span></span> | <span data-ttu-id="27623-1182">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1182">Default value</span></span> | <span data-ttu-id="27623-1183">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="27623-1184">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-1185">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1185">Timeout for server activity.</span></span> <span data-ttu-id="27623-1186">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-1187">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-1188">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="27623-1189">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1189">15 seconds</span></span> | <span data-ttu-id="27623-1190">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-1191">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="27623-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="27623-1192">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-1193">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-1193">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="27623-1194">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="27623-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="27623-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-1196">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1196">Option</span></span> | <span data-ttu-id="27623-1197">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1197">Default value</span></span> | <span data-ttu-id="27623-1198">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="27623-1199">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-1200">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1200">Timeout for server activity.</span></span> <span data-ttu-id="27623-1201">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="27623-1202">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-1203">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-1204">Java</span><span class="sxs-lookup"><span data-stu-id="27623-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-1205">Параметр</span><span class="sxs-lookup"><span data-stu-id="27623-1205">Option</span></span> | <span data-ttu-id="27623-1206">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1206">Default value</span></span> | <span data-ttu-id="27623-1207">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="27623-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="27623-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="27623-1209">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="27623-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="27623-1210">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1210">Timeout for server activity.</span></span> <span data-ttu-id="27623-1211">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-1212">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="27623-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="27623-1213">Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="27623-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="27623-1214">15 секунд</span><span class="sxs-lookup"><span data-stu-id="27623-1214">15 seconds</span></span> | <span data-ttu-id="27623-1215">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="27623-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="27623-1216">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="27623-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="27623-1217">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="27623-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="27623-1218">Дополнительные сведения о процессе подтверждения связи см. в статье [ :::no-loc(SignalR)::: Спецификация протокола концентратора](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="27623-1218">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="27623-1219">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="27623-1219">Configure additional options</span></span>

<span data-ttu-id="27623-1220">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="27623-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="27623-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="27623-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="27623-1222">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="27623-1222">.NET Option</span></span> |  <span data-ttu-id="27623-1223">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1223">Default value</span></span> | <span data-ttu-id="27623-1224">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="27623-1225">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="27623-1226">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1227">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1228">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1228">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="27623-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1229">Empty</span></span> | <span data-ttu-id="27623-1230">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="27623-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="27623-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1231">Empty</span></span> | <span data-ttu-id="27623-1232">Коллекция HTTP :::no-loc(cookie)::: s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1232">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="27623-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1233">Empty</span></span> | <span data-ttu-id="27623-1234">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="27623-1235">5 с</span><span class="sxs-lookup"><span data-stu-id="27623-1235">5 seconds</span></span> | <span data-ttu-id="27623-1236">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-1236">WebSockets only.</span></span> <span data-ttu-id="27623-1237">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="27623-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="27623-1238">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="27623-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="27623-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1239">Empty</span></span> | <span data-ttu-id="27623-1240">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="27623-1241">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="27623-1242">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="27623-1243">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="27623-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="27623-1244">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="27623-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="27623-1245">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как :::no-loc(Cookie)::: s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="27623-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="27623-1246">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="27623-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="27623-1247">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="27623-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="27623-1248">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="27623-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="27623-1249">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="27623-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="27623-1250">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="27623-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="27623-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="27623-1252">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="27623-1252">JavaScript Option</span></span> | <span data-ttu-id="27623-1253">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1253">Default Value</span></span> | <span data-ttu-id="27623-1254">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="27623-1255">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="27623-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="27623-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="27623-1257">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="27623-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="27623-1258">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1259">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1260">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1260">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="27623-1261">Java</span><span class="sxs-lookup"><span data-stu-id="27623-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="27623-1262">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="27623-1262">Java Option</span></span> | <span data-ttu-id="27623-1263">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="27623-1263">Default Value</span></span> | <span data-ttu-id="27623-1264">Описание</span><span class="sxs-lookup"><span data-stu-id="27623-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="27623-1265">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="27623-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="27623-1266">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="27623-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="27623-1267">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом** .</span><span class="sxs-lookup"><span data-stu-id="27623-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="27623-1268">Этот параметр нельзя включить при использовании :::no-loc(SignalR)::: службы Azure.</span><span class="sxs-lookup"><span data-stu-id="27623-1268">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="27623-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="27623-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="27623-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="27623-1270">Empty</span></span> | <span data-ttu-id="27623-1271">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="27623-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="27623-1272">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="27623-1273">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="27623-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="27623-1274">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="27623-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="27623-1275">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="27623-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
