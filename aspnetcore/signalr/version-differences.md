---
title: Различия между SignalR и ASP.NET CoreSignalR
author: bradygaster
description: Различия между SignalR и ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772583"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="d4852-103">Различия между ASP.NET SignalR и SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="d4852-104">ASP.NET Core SignalR несовместим с клиентами или серверами для ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="d4852-105">В этой статье подробно описаны функции, которые были удалены или изменены в ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="d4852-106">Как опознать версию SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="d4852-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-107">ASP.NET SignalR</span></span> | <span data-ttu-id="d4852-108">SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="d4852-109">Пакет NuGet сервера</span><span class="sxs-lookup"><span data-stu-id="d4852-109">Server NuGet package</span></span> | [<span data-ttu-id="d4852-110">Microsoft. AspNet. SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="d4852-111">Нет.</span><span class="sxs-lookup"><span data-stu-id="d4852-111">None.</span></span> <span data-ttu-id="d4852-112">Входит в состав общей платформы [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="d4852-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="d4852-113">Клиентские пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="d4852-113">Client NuGet packages</span></span> | [<span data-ttu-id="d4852-114">Microsoft. AspNet. SignalR. Client</span><span class="sxs-lookup"><span data-stu-id="d4852-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="d4852-115">Microsoft. AspNet. SignalR. JS</span><span class="sxs-lookup"><span data-stu-id="d4852-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="d4852-116">Microsoft. AspNetCore. SignalR. Client</span><span class="sxs-lookup"><span data-stu-id="d4852-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="d4852-117">Пакет NPM клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="d4852-117">JavaScript client npm package</span></span> | [<span data-ttu-id="d4852-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="d4852-119">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="d4852-119">Java client</span></span> | <span data-ttu-id="d4852-120">[Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)</span><span class="sxs-lookup"><span data-stu-id="d4852-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="d4852-121">Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="d4852-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="d4852-122">Тип серверного приложения</span><span class="sxs-lookup"><span data-stu-id="d4852-122">Server app type</span></span> | <span data-ttu-id="d4852-123">ASP.NET (System. Web) или OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="d4852-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="d4852-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-124">ASP.NET Core</span></span> |
| <span data-ttu-id="d4852-125">Поддерживаемые серверные платформы</span><span class="sxs-lookup"><span data-stu-id="d4852-125">Supported server platforms</span></span> | <span data-ttu-id="d4852-126">.NET Framework 4,5 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d4852-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="d4852-127">.NET Core 3,0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d4852-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="d4852-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-128">ASP.NET SignalR</span></span> | <span data-ttu-id="d4852-129">SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="d4852-130">Пакет NuGet сервера</span><span class="sxs-lookup"><span data-stu-id="d4852-130">Server NuGet package</span></span> | <span data-ttu-id="d4852-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="d4852-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="d4852-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d4852-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="d4852-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="d4852-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="d4852-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="d4852-134">(.NET Framework)</span></span> |
| <span data-ttu-id="d4852-135">Клиентские пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="d4852-135">Client NuGet packages</span></span> | <span data-ttu-id="d4852-136">[Microsoft. AspNet. SignalR. Компьютера](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="d4852-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="d4852-137">[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="d4852-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="d4852-138">[Microsoft. AspNetCore. SignalR. Компьютера](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="d4852-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="d4852-139">Пакет NPM клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="d4852-139">JavaScript client npm package</span></span> | [<span data-ttu-id="d4852-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="d4852-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="d4852-141">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="d4852-141">Java client</span></span> | <span data-ttu-id="d4852-142">[Репозиторий GitHub](https://github.com/SignalR/java-client) (не рекомендуется)</span><span class="sxs-lookup"><span data-stu-id="d4852-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="d4852-143">Пакет Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="d4852-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="d4852-144">Тип серверного приложения</span><span class="sxs-lookup"><span data-stu-id="d4852-144">Server app type</span></span> | <span data-ttu-id="d4852-145">ASP.NET (System. Web) или OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="d4852-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="d4852-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-146">ASP.NET Core</span></span> |
| <span data-ttu-id="d4852-147">Поддерживаемые серверные платформы</span><span class="sxs-lookup"><span data-stu-id="d4852-147">Supported server platforms</span></span> | <span data-ttu-id="d4852-148">.NET Framework 4,5 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d4852-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="d4852-149">.NET Framework 4.6.1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d4852-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="d4852-150">.NET Core 2,1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d4852-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="d4852-151">Различия в функциях</span><span class="sxs-lookup"><span data-stu-id="d4852-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="d4852-152">Автоматические повторное подключения</span><span class="sxs-lookup"><span data-stu-id="d4852-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4852-153">В ASP.NET SignalR:</span><span class="sxs-lookup"><span data-stu-id="d4852-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="d4852-154">По умолчанию SignalR пытается повторно подключиться к серверу при разрыве соединения.</span><span class="sxs-lookup"><span data-stu-id="d4852-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="d4852-155">В ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="d4852-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="d4852-156">Автоматические повторное подключение — это явное согласие с [клиентом .NET](xref:signalr/dotnet-client#automatically-reconnect) и [клиентом JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="d4852-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4852-157">До ASP.NET Core 3,0 SignalR не поддерживает автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="d4852-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="d4852-158">Если Клиент отключен, пользователь должен явно запустить новое подключение для повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="d4852-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="d4852-159">В ASP.NET SignalR SignalR пытается повторно подключиться к серверу при разрыве соединения.</span><span class="sxs-lookup"><span data-stu-id="d4852-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="d4852-160">Поддержка протоколов</span><span class="sxs-lookup"><span data-stu-id="d4852-160">Protocol support</span></span>

<span data-ttu-id="d4852-161">ASP.NET Core SignalR поддерживает JSON, а также новый двоичный протокол на основе [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="d4852-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="d4852-162">Кроме того, можно создавать пользовательские протоколы.</span><span class="sxs-lookup"><span data-stu-id="d4852-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="d4852-163">Транспорты</span><span class="sxs-lookup"><span data-stu-id="d4852-163">Transports</span></span>

<span data-ttu-id="d4852-164">Непрерывный многокадровый транспорт не поддерживается SignalRв ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4852-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="d4852-165">Различия на сервере</span><span class="sxs-lookup"><span data-stu-id="d4852-165">Differences on the server</span></span>

<span data-ttu-id="d4852-166">ASP.NET Core SignalR серверные библиотеки включены в [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), который используется в шаблоне **веб-приложения ASP.NET Core** для проектов Razor и и MVC.</span><span class="sxs-lookup"><span data-stu-id="d4852-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="d4852-167">ASP.NET Core SignalR — это ASP.NET Core по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d4852-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="d4852-168">Он должен быть настроен путем вызова <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4852-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4852-169">Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> внутри вызова метода в `Startup.Configure` методе.</span><span class="sxs-lookup"><span data-stu-id="d4852-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d4852-170">Чтобы настроить маршрутизацию, сопоставьте маршруты с концентраторами <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> внутри вызова метода в `Startup.Configure` методе.</span><span class="sxs-lookup"><span data-stu-id="d4852-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="d4852-171">Закрепленные сеансы</span><span class="sxs-lookup"><span data-stu-id="d4852-171">Sticky sessions</span></span>

<span data-ttu-id="d4852-172">Модель масштабирования для ASP.NET SignalR позволяет клиентам повторно подключаться и передавать сообщения на любой сервер в ферме.</span><span class="sxs-lookup"><span data-stu-id="d4852-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="d4852-173">В ASP.NET Core SignalRклиент должен взаимодействовать с тем же сервером на время соединения.</span><span class="sxs-lookup"><span data-stu-id="d4852-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="d4852-174">Для масштабирования с помощью Redis, это означает, что требуются прикрепленные сеансы.</span><span class="sxs-lookup"><span data-stu-id="d4852-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="d4852-175">Для масштабирования с помощью [службы SignalR Azure](/azure/azure-signalr/)прикрепленные сеансы не требуются, так как служба обрабатывает соединения с клиентами.</span><span class="sxs-lookup"><span data-stu-id="d4852-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="d4852-176">Один концентратор на подключение</span><span class="sxs-lookup"><span data-stu-id="d4852-176">Single hub per connection</span></span>

<span data-ttu-id="d4852-177">В ASP.NET Core SignalRмодель подключения была упрощена.</span><span class="sxs-lookup"><span data-stu-id="d4852-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="d4852-178">Подключения осуществляются непосредственно в одном концентраторе, а не в одном соединении, используемом для совместного доступа к нескольким концентраторам.</span><span class="sxs-lookup"><span data-stu-id="d4852-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="d4852-179">Потоковая передача</span><span class="sxs-lookup"><span data-stu-id="d4852-179">Streaming</span></span>

<span data-ttu-id="d4852-180">ASP.NET Core SignalR теперь поддерживает [потоковую передачу данных](xref:signalr/streaming) от концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="d4852-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="d4852-181">Область</span><span class="sxs-lookup"><span data-stu-id="d4852-181">State</span></span>

<span data-ttu-id="d4852-182">Удалена возможность передачи произвольного состояния между клиентами и концентратором (часто `HubState`называемым), а также поддержка сообщений о ходе выполнения.</span><span class="sxs-lookup"><span data-stu-id="d4852-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="d4852-183">В данный момент не существует аналога прокси-серверов концентратора.</span><span class="sxs-lookup"><span data-stu-id="d4852-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="d4852-184">Удаление Персистентконнектион</span><span class="sxs-lookup"><span data-stu-id="d4852-184">PersistentConnection removal</span></span>

<span data-ttu-id="d4852-185">В ASP.NET Core SignalRкласс [персистентконнектион](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) был удален.</span><span class="sxs-lookup"><span data-stu-id="d4852-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="d4852-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="d4852-186">GlobalHost</span></span>

<span data-ttu-id="d4852-187">ASP.NET Core в платформе встроена встраивание зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="d4852-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="d4852-188">Службы могут использовать DI для доступа к [хубконтекст](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="d4852-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="d4852-189">`GlobalHost` Объект, используемый в ASP.NET SignalR для получения, `HubContext` не существует в ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="d4852-190">хубпипелине</span><span class="sxs-lookup"><span data-stu-id="d4852-190">HubPipeline</span></span>

<span data-ttu-id="d4852-191">ASP.NET Core SignalR не поддерживает `HubPipeline` модули.</span><span class="sxs-lookup"><span data-stu-id="d4852-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="d4852-192">Различия в клиенте</span><span class="sxs-lookup"><span data-stu-id="d4852-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="d4852-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="d4852-193">TypeScript</span></span>

<span data-ttu-id="d4852-194">Клиент ASP.NET Core SignalR записывается в [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="d4852-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="d4852-195">При использовании [клиента JavaScript](xref:signalr/javascript-client)можно писать на JavaScript или TypeScript.</span><span class="sxs-lookup"><span data-stu-id="d4852-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="d4852-196">Клиент JavaScript размещается по адресу NPM</span><span class="sxs-lookup"><span data-stu-id="d4852-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4852-197">В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d4852-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="d4852-198">В ASP.NET Core версиях пакет [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM содержит библиотеки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d4852-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="d4852-199">Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="d4852-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d4852-200">Используйте NPM для получения и установки пакета `@microsoft/signalr` NPM.</span><span class="sxs-lookup"><span data-stu-id="d4852-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d4852-201">В ASP.NET версиях клиент JavaScript был получен с помощью пакета NuGet в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d4852-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="d4852-202">В ASP.NET Core версиях пакет [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM содержит библиотеки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d4852-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="d4852-203">Этот пакет не входит в шаблон **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="d4852-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d4852-204">Используйте NPM для получения и установки пакета `@aspnet/signalr` NPM.</span><span class="sxs-lookup"><span data-stu-id="d4852-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="d4852-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="d4852-205">jQuery</span></span>

<span data-ttu-id="d4852-206">Зависимость от jQuery удалена, однако проекты по-прежнему могут использовать jQuery.</span><span class="sxs-lookup"><span data-stu-id="d4852-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="d4852-207">Поддержка Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="d4852-207">Internet Explorer support</span></span>

<span data-ttu-id="d4852-208">Для SignalR ASP.NET Core требуется Microsoft Internet Explorer 11 или более поздняя версия (ASP.NET SignalR поддерживал Microsoft Internet Explorer 8 и более поздние версии).</span><span class="sxs-lookup"><span data-stu-id="d4852-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="d4852-209">Синтаксис клиентского метода JavaScript</span><span class="sxs-lookup"><span data-stu-id="d4852-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4852-210">Синтаксис JavaScript был изменен с версии ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="d4852-211">Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="d4852-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="d4852-212">Используйте метод [On](/javascript/api/@microsoft/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="d4852-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d4852-213">Синтаксис JavaScript был изменен с версии ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="d4852-214">Вместо использования `$connection` объекта создайте подключение с помощью API [хубконнектионбуилдер](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="d4852-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="d4852-215">Используйте метод [On](/javascript/api/@aspnet/signalr/HubConnection#on) , чтобы указать клиентские методы, которые может вызывать концентратор.</span><span class="sxs-lookup"><span data-stu-id="d4852-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="d4852-216">После создания клиентского метода запустите подключение концентратора.</span><span class="sxs-lookup"><span data-stu-id="d4852-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="d4852-217">Привязать метод [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) для ведения журнала или обработке ошибок.</span><span class="sxs-lookup"><span data-stu-id="d4852-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="d4852-218">Прокси-серверы концентратора</span><span class="sxs-lookup"><span data-stu-id="d4852-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4852-219">Прокси-серверы концентратора больше не создаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4852-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="d4852-220">Вместо этого имя метода передается в API [вызова](/javascript/api/@microsoft/signalr/hubconnection#invoke) в виде строки.</span><span class="sxs-lookup"><span data-stu-id="d4852-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d4852-221">Прокси-серверы концентратора больше не создаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4852-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="d4852-222">Вместо этого имя метода передается в API [вызова](/javascript/api/@aspnet/signalr/hubconnection#invoke) в виде строки.</span><span class="sxs-lookup"><span data-stu-id="d4852-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="d4852-223">.NET и другие клиенты</span><span class="sxs-lookup"><span data-stu-id="d4852-223">.NET and other clients</span></span>

<span data-ttu-id="d4852-224">Элемент [Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Пакет NuGet клиента содержит клиентские библиотеки .NET для ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="d4852-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="d4852-225">Используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> для создания и построения экземпляра соединения с концентратором.</span><span class="sxs-lookup"><span data-stu-id="d4852-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="d4852-226">Различия масштабирования</span><span class="sxs-lookup"><span data-stu-id="d4852-226">Scaleout differences</span></span>

<span data-ttu-id="d4852-227">ASP.NET SignalR поддерживает SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="d4852-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="d4852-228">ASP.NET Core SignalR поддерживает службу SignalR Azure и Redis.</span><span class="sxs-lookup"><span data-stu-id="d4852-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="d4852-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d4852-229">ASP.NET</span></span>

* <span data-ttu-id="d4852-230">[SignalRМасштабирование с помощью служебной шины Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="d4852-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="d4852-231">[SignalRМасштабирование с помощью Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="d4852-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="d4852-232">[SignalRМасштабирование с помощью SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="d4852-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="d4852-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4852-233">ASP.NET Core</span></span>

* <span data-ttu-id="d4852-234">[Служба SignalR Azure](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="d4852-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="d4852-235">Объединительная плата Redis</span><span class="sxs-lookup"><span data-stu-id="d4852-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="d4852-236">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d4852-236">Additional resources</span></span>

* [<span data-ttu-id="d4852-237">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="d4852-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d4852-238">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="d4852-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="d4852-239">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="d4852-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d4852-240">Поддерживаемые платформы</span><span class="sxs-lookup"><span data-stu-id="d4852-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
