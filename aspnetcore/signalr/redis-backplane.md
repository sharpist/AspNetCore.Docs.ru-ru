---
title: Redis Объединительная плата для SignalR горизонтального масштабирования ASP.NET Core
author: bradygaster
description: Узнайте, как настроить объединительную Redis, чтобы включить горизонтальное масштабирование для SignalR приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: bc28eb3096e88455347f68ca381c9a280d5a153e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633660"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a>Настройка объединительной платы Redis для SignalR горизонтального масштабирования ASP.NET Core

[Эндрю Стантон-медперсонала](https://twitter.com/anurse), [Брейди Гастер](https://twitter.com/bradygaster)и [Tom Dykstra)](https://github.com/tdykstra),

В этой статье описаны особенности SignalR настройки сервера [Redis](https://redis.io/) для масштабирования SignalR приложения ASP.NET Core.

## <a name="set-up-a-redis-backplane"></a>Настройка объединительной платы Redis

* Разверните сервер Redis.

  > [!IMPORTANT] 
  > Для использования в рабочей среде Redisная Объединительная плата рекомендуется только в том случае, если она работает в том же центре обработки данных, что и SignalR приложение. В противном случае задержка сети снижает производительность. Если SignalR приложение выполняется в облаке Azure, мы рекомендуем использовать службу Azure SignalR вместо объединительной платы Redis. Службу кэша Redis для Azure можно использовать для сред разработки и тестирования.

  Дополнительные сведения см. в следующих ресурсах:

  * <xref:signalr/scale>
  * [Документация по Redis](https://redis.io/)
  * [Документация по Кэшу Redis для Azure](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* В SignalR приложении установите `Microsoft.AspNetCore.SignalR.Redis` пакет NuGet.
* В `Startup.ConfigureServices` методе вызовите `AddRedis` после `AddSignalR` :

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* При необходимости настройте параметры:
 
  Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Параметры, указанные в параметре `ConfigurationOptions` reoverride, заданные в строке подключения.

  В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте. В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* В SignalR приложении установите один из следующих пакетов NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` — Зависит от StackExchange. Redis 2. X.X. Это рекомендуемый пакет для ASP.NET Core 2,2 и более поздних версий.
  * `Microsoft.AspNetCore.SignalR.Redis` — Зависит от StackExchange. Redis 1. X.X. Этот пакет не входит в ASP.NET Core 3,0 и более поздних версий.

* В `Startup.ConfigureServices` методе вызовите <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 При использовании `Microsoft.AspNetCore.SignalR.Redis` метода вызовите <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

* При необходимости настройте параметры:
 
  Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Параметры, указанные в параметре `ConfigurationOptions` reoverride, заданные в строке подключения.

  В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте. В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 При использовании `Microsoft.AspNetCore.SignalR.Redis` метода вызовите <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

  В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.

  Сведения о параметрах Redis см. в [документации по StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* В SignalR приложении установите следующий пакет NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* В `Startup.ConfigureServices` методе вызовите <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* При необходимости настройте параметры:
 
  Большинство параметров можно задать в строке соединения или в объекте [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Параметры, указанные в параметре `ConfigurationOptions` reoverride, заданные в строке подключения.

  В следующем примере показано, как задать параметры в `ConfigurationOptions` объекте. В этом примере добавляется префикс канала, чтобы несколько приложений могли совместно использовать один и тот же экземпляр Redis, как описано в следующем шаге.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  В приведенном выше коде `options.Configuration` инициализируется с помощью любого, указанного в строке подключения.

  Сведения о параметрах Redis см. в [документации по StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Если вы используете один сервер Redis для нескольких SignalR приложений, используйте разные префиксы канала для каждого SignalR приложения.

  Установка префикса канала изолирует одно SignalR приложение от других, использующих разные префиксы каналов. Если не назначить разные префиксы, сообщение, отправленное из одного приложения всем своим клиентам, будет передаваться всем клиентам всех приложений, использующих сервер Redis в качестве объединительной платы.

* Настройте программное обеспечение балансировки нагрузки фермы серверов для закрепленных сеансов. Ниже приведены некоторые примеры документации.

  * [Службы IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [пфсенсе](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Ошибки сервера Redis

Когда сервер Redis выйдет из строя, SignalR создает исключения, указывающие, что сообщения не будут доставлены. Некоторые типичные сообщения об исключениях:

* *Не удалось записать сообщение*
* *Не удалось вызвать метод концентратора "имя_метода"*
* *Сбой подключения к Redis*

SignalR не замещает сообщения для отправки при резервном копировании сервера. Все сообщения, отправленные во время работы сервера Redis, теряются.

SignalR автоматически повторно подключается, когда сервер Redis снова становится доступным.

### <a name="custom-behavior-for-connection-failures"></a>Настраиваемое поведение для сбоев подключения

Ниже приведен пример, демонстрирующий обработку событий сбоя подключения Redis.

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

## <a name="redis-clustering"></a>Кластеризация Redis

[Кластеризация Redis](https://redis.io/topics/cluster-spec) — это метод достижения высокого уровня доступности с помощью нескольких серверов Redis. Кластеризация официально не поддерживается, но может работать.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих ресурсах:

* <xref:signalr/scale>
* [Документация по Redis](https://redis.io/documentation)
* [Документация по StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Документация по Кэшу Redis для Azure](https://docs.microsoft.com/azure/redis-cache/)
