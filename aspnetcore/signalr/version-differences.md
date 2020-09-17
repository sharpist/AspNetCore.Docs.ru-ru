---
title: Различия между SignalR и ASP.NET Core SignalR
author: bradygaster
description: Различия между SignalR и ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: 965fbb3d8007cb64aaf51d82d87ed7a3a5298e9b
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721792"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="c85bc-103">Различия между ASP.NET SignalR и ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="c85bc-104">ASP.NET Core SignalR несовместим с клиентами или серверами для ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="c85bc-105">В этой статье описываются функции, которые были удалены или изменены в ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="c85bc-106">Определение SignalR версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="c85bc-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-107">ASP.NET SignalR</span></span> | <span data-ttu-id="c85bc-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="c85bc-109">**Пакет NuGet сервера**</span><span class="sxs-lookup"><span data-stu-id="c85bc-109">**Server NuGet package**</span></span> | <span data-ttu-id="c85bc-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="c85bc-111">Нет.</span><span class="sxs-lookup"><span data-stu-id="c85bc-111">None.</span></span> <span data-ttu-id="c85bc-112">Входит в состав общей платформы [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="c85bc-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="c85bc-113">**Клиентские пакеты NuGet**</span><span class="sxs-lookup"><span data-stu-id="c85bc-113">**Client NuGet packages**</span></span> | <span data-ttu-id="c85bc-114">[Microsoft. AspNet. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="c85bc-115">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="c85bc-116">[Microsoft. AspNetCore. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="c85bc-117">**Пакет NPM клиента JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c85bc-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="c85bc-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="c85bc-119">**Клиент Java**</span><span class="sxs-lookup"><span data-stu-id="c85bc-119">**Java client**</span></span> | <span data-ttu-id="c85bc-120">[Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)</span><span class="sxs-lookup"><span data-stu-id="c85bc-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="c85bc-121">Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="c85bc-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="c85bc-122">**Тип серверного приложения**</span><span class="sxs-lookup"><span data-stu-id="c85bc-122">**Server app type**</span></span> | <span data-ttu-id="c85bc-123">ASP.NET (System. Web) или OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="c85bc-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="c85bc-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c85bc-124">ASP.NET Core</span></span> |
| <span data-ttu-id="c85bc-125">**Поддерживаемые серверные платформы**</span><span class="sxs-lookup"><span data-stu-id="c85bc-125">**Supported server platforms**</span></span> | <span data-ttu-id="c85bc-126">.NET Framework 4,5 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="c85bc-127">.NET Core 3,0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="c85bc-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-128">ASP.NET SignalR</span></span> | <span data-ttu-id="c85bc-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="c85bc-130">**Пакет NuGet сервера**</span><span class="sxs-lookup"><span data-stu-id="c85bc-130">**Server NuGet package**</span></span> | <span data-ttu-id="c85bc-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="c85bc-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c85bc-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="c85bc-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="c85bc-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="c85bc-134">(.NET Framework)</span></span> |
| <span data-ttu-id="c85bc-135">**Клиентские пакеты NuGet**</span><span class="sxs-lookup"><span data-stu-id="c85bc-135">**Client NuGet packages**</span></span> | <span data-ttu-id="c85bc-136">[Microsoft. AspNet. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="c85bc-137">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="c85bc-138">[Microsoft. AspNetCore. SignalR . Компьютера](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c85bc-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="c85bc-139">**Пакет NPM клиента JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c85bc-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="c85bc-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="c85bc-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="c85bc-141">**Клиент Java**</span><span class="sxs-lookup"><span data-stu-id="c85bc-141">**Java client**</span></span> | <span data-ttu-id="c85bc-142">[Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)</span><span class="sxs-lookup"><span data-stu-id="c85bc-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="c85bc-143">Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="c85bc-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="c85bc-144">**Тип серверного приложения**</span><span class="sxs-lookup"><span data-stu-id="c85bc-144">**Server app type**</span></span> | <span data-ttu-id="c85bc-145">ASP.NET (System. Web) или OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="c85bc-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="c85bc-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c85bc-146">ASP.NET Core</span></span> |
| <span data-ttu-id="c85bc-147">**Поддерживаемые серверные платформы**</span><span class="sxs-lookup"><span data-stu-id="c85bc-147">**Supported server platforms**</span></span> | <span data-ttu-id="c85bc-148">.NET Framework 4,5 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="c85bc-149">.NET Framework 4.6.1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="c85bc-150">.NET Core 2,1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="c85bc-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="c85bc-151">Различия в функциях</span><span class="sxs-lookup"><span data-stu-id="c85bc-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="c85bc-152">Автоматические повторное подключения</span><span class="sxs-lookup"><span data-stu-id="c85bc-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c85bc-153">В ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="c85bc-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="c85bc-154">По умолчанию SignalR пытается повторно подключиться к серверу при разрыве соединения.</span><span class="sxs-lookup"><span data-stu-id="c85bc-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="c85bc-155">В ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="c85bc-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="c85bc-156">Автоматические повторное подключение — это явное согласие с [клиентом .NET](xref:signalr/dotnet-client#automatically-reconnect) и [клиентом JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="c85bc-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c85bc-157">До ASP.NET Core 3,0 SignalR не поддерживает автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="c85bc-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="c85bc-158">Если Клиент отключен, пользователь должен явно запустить новое подключение для повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="c85bc-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="c85bc-159">В ASP.NET SignalR SignalR пытается повторно подключиться к серверу при разрыве соединения.</span><span class="sxs-lookup"><span data-stu-id="c85bc-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="c85bc-160">Поддержка протоколов</span><span class="sxs-lookup"><span data-stu-id="c85bc-160">Protocol support</span></span>

<span data-ttu-id="c85bc-161">ASP.NET Core SignalR поддерживает JSON, а также новый двоичный протокол на основе [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="c85bc-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="c85bc-162">Кроме того, можно создавать пользовательские протоколы.</span><span class="sxs-lookup"><span data-stu-id="c85bc-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="c85bc-163">Транспорты</span><span class="sxs-lookup"><span data-stu-id="c85bc-163">Transports</span></span>

<span data-ttu-id="c85bc-164">Непрерывный многокадровый транспорт не поддерживается в ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="c85bc-165">Различия на сервере</span><span class="sxs-lookup"><span data-stu-id="c85bc-165">Differences on the server</span></span>

<span data-ttu-id="c85bc-166">ASP.NET Core SignalR серверные библиотеки включены в [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), который используется в шаблоне **веб-приложения ASP.NET Core** для Razor проектов и и MVC.</span><span class="sxs-lookup"><span data-stu-id="c85bc-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="c85bc-167">ASP.NET Core SignalR — это ASP.NET Core по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c85bc-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="c85bc-168">Он должен быть настроен путем вызова <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c85bc-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c85bc-169">Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами внутри <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> вызова метода в `Startup.Configure` методе.</span><span class="sxs-lookup"><span data-stu-id="c85bc-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c85bc-170">Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами внутри <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> вызова метода в `Startup.Configure` методе.</span><span class="sxs-lookup"><span data-stu-id="c85bc-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="c85bc-171">Закрепленные сеансы</span><span class="sxs-lookup"><span data-stu-id="c85bc-171">Sticky sessions</span></span>

<span data-ttu-id="c85bc-172">Модель масштабирования для ASP.NET SignalR позволяет клиентам повторно подключаться и передавать сообщения на любой сервер в ферме.</span><span class="sxs-lookup"><span data-stu-id="c85bc-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="c85bc-173">В ASP.NET Core SignalR клиент должен взаимодействовать с тем же сервером на время соединения.</span><span class="sxs-lookup"><span data-stu-id="c85bc-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="c85bc-174">Для масштабирования с помощью Redis, это означает, что требуются прикрепленные сеансы.</span><span class="sxs-lookup"><span data-stu-id="c85bc-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="c85bc-175">Для масштабирования с помощью [ SignalR службы Azure](/azure/azure-signalr/)прикрепленные сеансы не требуются, так как служба обрабатывает соединения с клиентами.</span><span class="sxs-lookup"><span data-stu-id="c85bc-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="c85bc-176">Один концентратор на подключение</span><span class="sxs-lookup"><span data-stu-id="c85bc-176">Single hub per connection</span></span>

<span data-ttu-id="c85bc-177">В ASP.NET Core SignalR модель подключения была упрощена.</span><span class="sxs-lookup"><span data-stu-id="c85bc-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="c85bc-178">Подключения осуществляются непосредственно в одном концентраторе, а не в одном соединении, используемом для совместного доступа к нескольким концентраторам.</span><span class="sxs-lookup"><span data-stu-id="c85bc-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="c85bc-179">Потоковые операторы</span><span class="sxs-lookup"><span data-stu-id="c85bc-179">Streaming</span></span>

<span data-ttu-id="c85bc-180">ASP.NET Core SignalR теперь поддерживает [потоковую передачу данных](xref:signalr/streaming) от концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="c85bc-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="c85bc-181">Состояние</span><span class="sxs-lookup"><span data-stu-id="c85bc-181">State</span></span>

<span data-ttu-id="c85bc-182">Удалена возможность передачи произвольного состояния между клиентами и концентратором (часто называемым `HubState` ), а также поддержка сообщений о ходе выполнения.</span><span class="sxs-lookup"><span data-stu-id="c85bc-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="c85bc-183">В данный момент не существует аналога прокси-серверов концентратора.</span><span class="sxs-lookup"><span data-stu-id="c85bc-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="c85bc-184">Удаление Персистентконнектион</span><span class="sxs-lookup"><span data-stu-id="c85bc-184">PersistentConnection removal</span></span>

<span data-ttu-id="c85bc-185">В ASP.NET Core SignalR класс [персистентконнектион](/previous-versions/aspnet/jj919047(v=vs.118)) был удален.</span><span class="sxs-lookup"><span data-stu-id="c85bc-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="c85bc-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="c85bc-186">GlobalHost</span></span>

<span data-ttu-id="c85bc-187">ASP.NET Core в платформе встроена встраивание зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="c85bc-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="c85bc-188">Службы могут использовать DI для доступа к [хубконтекст](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="c85bc-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="c85bc-189">`GlobalHost`Объект, используемый в ASP.NET SignalR для получения, `HubContext` не существует в ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="c85bc-190">хубпипелине</span><span class="sxs-lookup"><span data-stu-id="c85bc-190">HubPipeline</span></span>

<span data-ttu-id="c85bc-191">ASP.NET Core SignalR не поддерживает `HubPipeline` модули.</span><span class="sxs-lookup"><span data-stu-id="c85bc-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="c85bc-192">Различия в клиенте</span><span class="sxs-lookup"><span data-stu-id="c85bc-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="c85bc-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="c85bc-193">TypeScript</span></span>

<span data-ttu-id="c85bc-194">Клиент ASP.NET Core SignalR записывается в [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="c85bc-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="c85bc-195">При использовании [клиента JavaScript](xref:signalr/javascript-client)можно писать на JavaScript или TypeScript.</span><span class="sxs-lookup"><span data-stu-id="c85bc-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="c85bc-196">Клиент JavaScript размещается по адресу NPM</span><span class="sxs-lookup"><span data-stu-id="c85bc-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c85bc-197">В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c85bc-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="c85bc-198">В ASP.NET Core версиях [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) пакет NPM содержит библиотеки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c85bc-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="c85bc-199">Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="c85bc-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c85bc-200">Используйте NPM для получения и установки `@microsoft/signalr` пакета NPM.</span><span class="sxs-lookup"><span data-stu-id="c85bc-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c85bc-201">В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c85bc-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="c85bc-202">В ASP.NET Core версиях [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) пакет NPM содержит библиотеки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c85bc-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="c85bc-203">Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="c85bc-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c85bc-204">Используйте NPM для получения и установки `@aspnet/signalr` пакета NPM.</span><span class="sxs-lookup"><span data-stu-id="c85bc-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="c85bc-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="c85bc-205">jQuery</span></span>

<span data-ttu-id="c85bc-206">Зависимость от jQuery удалена, однако проекты по-прежнему могут использовать jQuery.</span><span class="sxs-lookup"><span data-stu-id="c85bc-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="c85bc-207">Поддержка Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c85bc-207">Internet Explorer support</span></span>

<span data-ttu-id="c85bc-208">Для ASP.NET Core SignalR требуется Microsoft Internet Explorer 11 или более поздняя версия (ASP.NET SignalR поддерживал Microsoft Internet Explorer 8 и более поздние версии).</span><span class="sxs-lookup"><span data-stu-id="c85bc-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="c85bc-209">Синтаксис клиентского метода JavaScript</span><span class="sxs-lookup"><span data-stu-id="c85bc-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c85bc-210">Синтаксис JavaScript был изменен с версии ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="c85bc-211">Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="c85bc-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="c85bc-212">Используйте метод [On](/javascript/api/@microsoft/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="c85bc-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c85bc-213">Синтаксис JavaScript был изменен с версии ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="c85bc-214">Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="c85bc-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="c85bc-215">Используйте метод [On](/javascript/api/@aspnet/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="c85bc-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="c85bc-216">После создания клиентского метода запустите подключение концентратора.</span><span class="sxs-lookup"><span data-stu-id="c85bc-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="c85bc-217">Привязать метод [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) для ведения журнала или обработке ошибок.</span><span class="sxs-lookup"><span data-stu-id="c85bc-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="c85bc-218">Прокси-серверы концентратора</span><span class="sxs-lookup"><span data-stu-id="c85bc-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c85bc-219">Прокси-серверы концентратора больше не создаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="c85bc-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="c85bc-220">Вместо этого имя метода передается в API [вызова](/javascript/api/@microsoft/signalr/hubconnection#invoke) в виде строки.</span><span class="sxs-lookup"><span data-stu-id="c85bc-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c85bc-221">Прокси-серверы концентратора больше не создаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="c85bc-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="c85bc-222">Вместо этого имя метода передается в API [вызова](/javascript/api/@aspnet/signalr/hubconnection#invoke) в виде строки.</span><span class="sxs-lookup"><span data-stu-id="c85bc-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="c85bc-223">.NET и другие клиенты</span><span class="sxs-lookup"><span data-stu-id="c85bc-223">.NET and other clients</span></span>

<span data-ttu-id="c85bc-224">Элемент [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Пакет NuGet клиента содержит клиентские библиотеки .NET для ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="c85bc-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="c85bc-225">Используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> для создания и построения экземпляра соединения с концентратором.</span><span class="sxs-lookup"><span data-stu-id="c85bc-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="c85bc-226">Различия масштабирования</span><span class="sxs-lookup"><span data-stu-id="c85bc-226">Scaleout differences</span></span>

<span data-ttu-id="c85bc-227">ASP.NET SignalR поддерживает SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="c85bc-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="c85bc-228">ASP.NET Core SignalR поддерживает SignalR службу Azure и Redis.</span><span class="sxs-lookup"><span data-stu-id="c85bc-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="c85bc-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c85bc-229">ASP.NET</span></span>

* [<span data-ttu-id="c85bc-230">SignalR Масштабирование с помощью служебной шины Azure</span><span class="sxs-lookup"><span data-stu-id="c85bc-230">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="c85bc-231">SignalR Масштабирование с помощью Redis</span><span class="sxs-lookup"><span data-stu-id="c85bc-231">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="c85bc-232">SignalR Масштабирование с помощью SQL Server</span><span class="sxs-lookup"><span data-stu-id="c85bc-232">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="c85bc-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c85bc-233">ASP.NET Core</span></span>

* [<span data-ttu-id="c85bc-234">SignalRСлужба Azure</span><span class="sxs-lookup"><span data-stu-id="c85bc-234">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="c85bc-235">Объединительная плата Redis</span><span class="sxs-lookup"><span data-stu-id="c85bc-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="c85bc-236">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c85bc-236">Additional resources</span></span>

* [<span data-ttu-id="c85bc-237">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="c85bc-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c85bc-238">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="c85bc-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c85bc-239">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="c85bc-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c85bc-240">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="c85bc-240">Supported platforms</span></span>](xref:signalr/supported-platforms)