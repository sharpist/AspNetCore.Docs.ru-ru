---
title: Redis Объединительная плата для горизонтального SignalR масштабирования ASP.NET Core
author: bradygaster
description: Узнайте, как настроить объединительную Redis, чтобы включить горизонтальное масштабирование для приложения ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775353"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="7064e-103">Настройка объединительной платы Redis для горизонтального масштабирования ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7064e-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="7064e-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse), [Брейди Гастер](https://twitter.com/bradygaster)и [Tom Dykstra)](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="7064e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="7064e-105">В этой статье SignalRописаны особенности настройки сервера [Redis](https://redis.io/) для масштабирования приложения ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7064e-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="7064e-106">Настройка объединительной платы Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="7064e-107">Разверните сервер Redis.</span><span class="sxs-lookup"><span data-stu-id="7064e-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="7064e-108">Для использования в рабочей среде Redisная Объединительная плата рекомендуется только в том случае, если она работает в том же центре SignalR обработки данных, что и приложение.</span><span class="sxs-lookup"><span data-stu-id="7064e-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="7064e-109">В противном случае задержка сети снижает производительность.</span><span class="sxs-lookup"><span data-stu-id="7064e-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="7064e-110">Если SignalR приложение выполняется в облаке Azure, мы рекомендуем использовать службу Azure SignalR вместо объединительной платы Redis.</span><span class="sxs-lookup"><span data-stu-id="7064e-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="7064e-111">Службу кэша Redis для Azure можно использовать для сред разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="7064e-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="7064e-112">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="7064e-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="7064e-113">Документация по Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="7064e-114">Документация по Кэшу Redis для Azure</span><span class="sxs-lookup"><span data-stu-id="7064e-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="7064e-115">В SignalR приложении установите пакет `Microsoft.AspNetCore.SignalR.Redis` NuGet.</span><span class="sxs-lookup"><span data-stu-id="7064e-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="7064e-116">В `Startup.ConfigureServices` методе вызовите `AddRedis` после `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="7064e-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="7064e-117">При необходимости настройте параметры:</span><span class="sxs-lookup"><span data-stu-id="7064e-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="7064e-118">Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="7064e-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7064e-119">Параметры, указанные `ConfigurationOptions` в параметре reoverride, заданные в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7064e-120">В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте.</span><span class="sxs-lookup"><span data-stu-id="7064e-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7064e-121">В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="7064e-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="7064e-122">В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="7064e-123">В SignalR приложении установите один из следующих пакетов NuGet:</span><span class="sxs-lookup"><span data-stu-id="7064e-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="7064e-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`— Зависит от StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="7064e-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="7064e-125">Это рекомендуемый пакет для ASP.NET Core 2,2 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="7064e-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="7064e-126">`Microsoft.AspNetCore.SignalR.Redis`— Зависит от StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="7064e-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="7064e-127">Этот пакет не входит в ASP.NET Core 3,0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="7064e-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="7064e-128">В `Startup.ConfigureServices` методе вызовите <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="7064e-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="7064e-129">При использовании `Microsoft.AspNetCore.SignalR.Redis`метода вызовите <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span><span class="sxs-lookup"><span data-stu-id="7064e-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="7064e-130">При необходимости настройте параметры:</span><span class="sxs-lookup"><span data-stu-id="7064e-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="7064e-131">Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="7064e-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7064e-132">Параметры, указанные `ConfigurationOptions` в параметре reoverride, заданные в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7064e-133">В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте.</span><span class="sxs-lookup"><span data-stu-id="7064e-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7064e-134">В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="7064e-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="7064e-135">При использовании `Microsoft.AspNetCore.SignalR.Redis`метода вызовите <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span><span class="sxs-lookup"><span data-stu-id="7064e-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="7064e-136">В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="7064e-137">Сведения о параметрах Redis см. в [документации по StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="7064e-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7064e-138">В SignalR приложении установите следующий пакет NuGet:</span><span class="sxs-lookup"><span data-stu-id="7064e-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="7064e-139">В `Startup.ConfigureServices` методе вызовите <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="7064e-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="7064e-140">При необходимости настройте параметры:</span><span class="sxs-lookup"><span data-stu-id="7064e-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="7064e-141">Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="7064e-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="7064e-142">Параметры, указанные `ConfigurationOptions` в параметре reoverride, заданные в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="7064e-143">В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте.</span><span class="sxs-lookup"><span data-stu-id="7064e-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="7064e-144">В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="7064e-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="7064e-145">В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.</span><span class="sxs-lookup"><span data-stu-id="7064e-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="7064e-146">Сведения о параметрах Redis см. в [документации по StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="7064e-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="7064e-147">Если вы используете один сервер Redis для нескольких SignalR приложений, используйте разные префиксы канала для каждого SignalR приложения.</span><span class="sxs-lookup"><span data-stu-id="7064e-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="7064e-148">Установка префикса канала изолирует одно SignalR приложение от других, использующих разные префиксы каналов.</span><span class="sxs-lookup"><span data-stu-id="7064e-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="7064e-149">Если не назначить разные префиксы, сообщение, отправленное из одного приложения всем своим клиентам, будет передаваться всем клиентам всех приложений, использующих сервер Redis в качестве объединительной платы.</span><span class="sxs-lookup"><span data-stu-id="7064e-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="7064e-150">Настройте программное обеспечение балансировки нагрузки фермы серверов для закрепленных сеансов.</span><span class="sxs-lookup"><span data-stu-id="7064e-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="7064e-151">Ниже приведены некоторые примеры документации.</span><span class="sxs-lookup"><span data-stu-id="7064e-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="7064e-152">IIS</span><span class="sxs-lookup"><span data-stu-id="7064e-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="7064e-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="7064e-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="7064e-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="7064e-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="7064e-155">пфсенсе</span><span class="sxs-lookup"><span data-stu-id="7064e-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="7064e-156">Ошибки сервера Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-156">Redis server errors</span></span>

<span data-ttu-id="7064e-157">Когда сервер Redis выйдет из строя SignalR , создает исключения, указывающие, что сообщения не будут доставлены.</span><span class="sxs-lookup"><span data-stu-id="7064e-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="7064e-158">Некоторые типичные сообщения об исключениях:</span><span class="sxs-lookup"><span data-stu-id="7064e-158">Some typical exception messages:</span></span>

* <span data-ttu-id="7064e-159">*Не удалось записать сообщение*</span><span class="sxs-lookup"><span data-stu-id="7064e-159">*Failed writing message*</span></span>
* <span data-ttu-id="7064e-160">*Не удалось вызвать метод концентратора "имя_метода"*</span><span class="sxs-lookup"><span data-stu-id="7064e-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="7064e-161">*Сбой подключения к Redis*</span><span class="sxs-lookup"><span data-stu-id="7064e-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="7064e-162">не замещает сообщения для отправки при резервном копировании сервера.</span><span class="sxs-lookup"><span data-stu-id="7064e-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="7064e-163">Все сообщения, отправленные во время работы сервера Redis, теряются.</span><span class="sxs-lookup"><span data-stu-id="7064e-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="7064e-164">автоматически повторно подключается, когда сервер Redis снова становится доступным.</span><span class="sxs-lookup"><span data-stu-id="7064e-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="7064e-165">Настраиваемое поведение для сбоев подключения</span><span class="sxs-lookup"><span data-stu-id="7064e-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="7064e-166">Ниже приведен пример, демонстрирующий обработку событий сбоя подключения Redis.</span><span class="sxs-lookup"><span data-stu-id="7064e-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="7064e-167">Кластеризация Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-167">Redis Clustering</span></span>

<span data-ttu-id="7064e-168">[Кластеризация Redis](https://redis.io/topics/cluster-spec) — это метод достижения высокого уровня доступности с помощью нескольких серверов Redis.</span><span class="sxs-lookup"><span data-stu-id="7064e-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="7064e-169">Кластеризация официально не поддерживается, но может работать.</span><span class="sxs-lookup"><span data-stu-id="7064e-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7064e-170">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="7064e-170">Next steps</span></span>

<span data-ttu-id="7064e-171">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="7064e-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="7064e-172">Документация по Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="7064e-173">Документация по StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="7064e-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="7064e-174">Документация по Кэшу Redis для Azure</span><span class="sxs-lookup"><span data-stu-id="7064e-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
