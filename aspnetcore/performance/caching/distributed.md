---
title: Распределенное кэширование в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать распределенный кэш ASP.NET Core для повышения производительности и масштабируемости приложений, особенно в среде облака или фермы серверов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 56c67178bd5c63f08a812357a4f8e672dd483994
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405397"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="eac96-103">Распределенное кэширование в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eac96-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="eac96-104">[Мохсин Насир](https://github.com/mohsinnasir) и [Стив Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="eac96-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eac96-105">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="eac96-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="eac96-106">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="eac96-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="eac96-107">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="eac96-108">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="eac96-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="eac96-109">— *coherent* Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="eac96-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="eac96-110">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="eac96-111">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="eac96-111">Doesn't use local memory.</span></span>

<span data-ttu-id="eac96-112">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="eac96-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="eac96-113">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="eac96-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="eac96-114">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="eac96-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="eac96-115">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="eac96-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="eac96-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eac96-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eac96-117">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="eac96-117">Prerequisites</span></span>

<span data-ttu-id="eac96-118">Чтобы использовать SQL Server распределенный кэш, добавьте ссылку на пакет в пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="eac96-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="eac96-119">Чтобы использовать распределенный кэш Redis, добавьте ссылку на пакет в пакет [Microsoft. Extensions. Caching. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="eac96-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="eac96-120">Чтобы использовать распределенный кэш NCache, добавьте ссылку на пакет в пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="eac96-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="eac96-121">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="eac96-121">IDistributedCache interface</span></span>

<span data-ttu-id="eac96-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="eac96-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="eac96-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="eac96-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="eac96-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="eac96-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="eac96-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="eac96-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="eac96-127">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="eac96-127">Establish distributed caching services</span></span>

<span data-ttu-id="eac96-128">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eac96-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eac96-129">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="eac96-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="eac96-130">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="eac96-131">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="eac96-132">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="eac96-133">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="eac96-134">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-134">Distributed Memory Cache</span></span>

<span data-ttu-id="eac96-135">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="eac96-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="eac96-136">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="eac96-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="eac96-137">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="eac96-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="eac96-138">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="eac96-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="eac96-139">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="eac96-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="eac96-140">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="eac96-141">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="eac96-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="eac96-142">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="eac96-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="eac96-143">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="eac96-144">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="eac96-145">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="eac96-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="eac96-146">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="eac96-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="eac96-147">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="eac96-148">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="eac96-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="eac96-149">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="eac96-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="eac96-150">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="eac96-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="eac96-151">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="eac96-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="eac96-153">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="eac96-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="eac96-154">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="eac96-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или вappsettings.jsв *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="eac96-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="eac96-156">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="eac96-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="eac96-157">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-157">Distributed Redis Cache</span></span>

<span data-ttu-id="eac96-158">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="eac96-159">Вы можете использовать Redis локально, и вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure.</span><span class="sxs-lookup"><span data-stu-id="eac96-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="eac96-160">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="eac96-161">Чтобы установить Redis на локальном компьютере, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="eac96-162">Установите [пакет шоколадного Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="eac96-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="eac96-163">Запустите `redis-server` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="eac96-164">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-164">Distributed NCache Cache</span></span>

<span data-ttu-id="eac96-165">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eac96-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="eac96-166">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="eac96-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="eac96-167">Чтобы установить и настроить NCache на локальном компьютере, ознакомьтесь с [руководством по NCache начало работы для Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="eac96-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="eac96-168">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-168">To configure NCache:</span></span>

1. <span data-ttu-id="eac96-169">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="eac96-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="eac96-170">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="eac96-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="eac96-171">Добавьте следующий код в файл `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eac96-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="eac96-172">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="eac96-172">Use the distributed cache</span></span>

<span data-ttu-id="eac96-173">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="eac96-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="eac96-174">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eac96-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="eac96-175">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eac96-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="eac96-176">Текущее время кэшируется с помощью <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Дополнительные сведения см. в разделе [универсальный узел: ихостаппликатионлифетиме](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="eac96-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="eac96-177">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="eac96-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="eac96-178">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="eac96-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="eac96-179">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="eac96-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="eac96-180">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="eac96-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="eac96-181">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="eac96-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="eac96-182">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="eac96-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="eac96-183">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="eac96-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="eac96-184">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="eac96-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="eac96-185">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="eac96-185">Recommendations</span></span>

<span data-ttu-id="eac96-186">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="eac96-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="eac96-187">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="eac96-187">Existing infrastructure</span></span>
* <span data-ttu-id="eac96-188">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="eac96-188">Performance requirements</span></span>
* <span data-ttu-id="eac96-189">Стоимость</span><span class="sxs-lookup"><span data-stu-id="eac96-189">Cost</span></span>
* <span data-ttu-id="eac96-190">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="eac96-190">Team experience</span></span>

<span data-ttu-id="eac96-191">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="eac96-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="eac96-192">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="eac96-193">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="eac96-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="eac96-194">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="eac96-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="eac96-195">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="eac96-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="eac96-196">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eac96-197">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="eac96-197">Additional resources</span></span>

* [<span data-ttu-id="eac96-198">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="eac96-199">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="eac96-200">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="eac96-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="eac96-201">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="eac96-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="eac96-202">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="eac96-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="eac96-203">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="eac96-204">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="eac96-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="eac96-205">— *coherent* Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="eac96-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="eac96-206">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="eac96-207">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="eac96-207">Doesn't use local memory.</span></span>

<span data-ttu-id="eac96-208">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="eac96-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="eac96-209">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="eac96-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="eac96-210">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="eac96-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="eac96-211">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="eac96-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="eac96-212">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eac96-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eac96-213">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="eac96-213">Prerequisites</span></span>

<span data-ttu-id="eac96-214">Чтобы использовать SQL Server распределенный кэш, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="eac96-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="eac96-215">Чтобы использовать распределенный кэш Redis, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [Microsoft. Extensions. Caching. стаккексчанжередис](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="eac96-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="eac96-216">Пакет Redis не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет Redis в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="eac96-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="eac96-217">Чтобы использовать распределенный кэш NCache, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="eac96-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="eac96-218">Пакет NCache не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет NCache в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="eac96-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="eac96-219">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="eac96-219">IDistributedCache interface</span></span>

<span data-ttu-id="eac96-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="eac96-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="eac96-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="eac96-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="eac96-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="eac96-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="eac96-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="eac96-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="eac96-225">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="eac96-225">Establish distributed caching services</span></span>

<span data-ttu-id="eac96-226">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eac96-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eac96-227">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="eac96-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="eac96-228">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="eac96-229">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="eac96-230">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="eac96-231">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="eac96-232">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-232">Distributed Memory Cache</span></span>

<span data-ttu-id="eac96-233">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="eac96-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="eac96-234">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="eac96-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="eac96-235">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="eac96-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="eac96-236">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="eac96-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="eac96-237">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="eac96-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="eac96-238">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="eac96-239">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="eac96-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="eac96-240">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="eac96-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="eac96-241">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="eac96-242">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="eac96-243">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="eac96-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="eac96-244">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="eac96-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="eac96-245">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="eac96-246">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="eac96-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="eac96-247">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="eac96-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="eac96-248">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="eac96-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="eac96-249">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="eac96-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="eac96-251">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="eac96-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="eac96-252">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="eac96-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или вappsettings.jsв *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="eac96-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="eac96-254">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="eac96-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="eac96-255">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-255">Distributed Redis Cache</span></span>

<span data-ttu-id="eac96-256">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="eac96-257">Вы можете использовать Redis локально, и вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure.</span><span class="sxs-lookup"><span data-stu-id="eac96-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="eac96-258">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="eac96-259">Чтобы установить Redis на локальном компьютере, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="eac96-260">Установите [пакет шоколадного Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="eac96-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="eac96-261">Запустите `redis-server` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="eac96-262">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-262">Distributed NCache Cache</span></span>

<span data-ttu-id="eac96-263">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eac96-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="eac96-264">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="eac96-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="eac96-265">Чтобы установить и настроить NCache на локальном компьютере, ознакомьтесь с [руководством по NCache начало работы для Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="eac96-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="eac96-266">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-266">To configure NCache:</span></span>

1. <span data-ttu-id="eac96-267">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="eac96-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="eac96-268">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="eac96-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="eac96-269">Добавьте следующий код в файл `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eac96-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="eac96-270">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="eac96-270">Use the distributed cache</span></span>

<span data-ttu-id="eac96-271">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="eac96-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="eac96-272">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eac96-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="eac96-273">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eac96-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="eac96-274">Текущее время кэшируется с помощью <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Дополнительные сведения см. в разделе [веб-узел: иаппликатионлифетиме Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="eac96-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="eac96-275">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="eac96-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="eac96-276">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="eac96-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="eac96-277">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="eac96-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="eac96-278">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="eac96-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="eac96-279">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="eac96-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="eac96-280">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="eac96-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="eac96-281">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="eac96-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="eac96-282">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="eac96-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="eac96-283">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="eac96-283">Recommendations</span></span>

<span data-ttu-id="eac96-284">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="eac96-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="eac96-285">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="eac96-285">Existing infrastructure</span></span>
* <span data-ttu-id="eac96-286">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="eac96-286">Performance requirements</span></span>
* <span data-ttu-id="eac96-287">Стоимость</span><span class="sxs-lookup"><span data-stu-id="eac96-287">Cost</span></span>
* <span data-ttu-id="eac96-288">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="eac96-288">Team experience</span></span>

<span data-ttu-id="eac96-289">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="eac96-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="eac96-290">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="eac96-291">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="eac96-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="eac96-292">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="eac96-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="eac96-293">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="eac96-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="eac96-294">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eac96-295">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="eac96-295">Additional resources</span></span>

* [<span data-ttu-id="eac96-296">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="eac96-297">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="eac96-298">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="eac96-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="eac96-299">Распределенный кэш — это кэш, совместно используемый несколькими серверами приложений, обычно поддерживаемый как внешняя служба для серверов приложений, обращающихся к ней.</span><span class="sxs-lookup"><span data-stu-id="eac96-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="eac96-300">Распределенный кэш может повысить производительность и масштабируемость приложения ASP.NET Core, особенно если приложение размещено в облачной службе или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="eac96-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="eac96-301">Распределенный кэш имеет несколько преимуществ по сравнению с другими сценариями кэширования, в которых кэшированные данные хранятся на отдельных серверах приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="eac96-302">При распределении кэшированных данных данные:</span><span class="sxs-lookup"><span data-stu-id="eac96-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="eac96-303">— *coherent* Согласованность между запросами к нескольким серверам.</span><span class="sxs-lookup"><span data-stu-id="eac96-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="eac96-304">Выдерживает перезапуски сервера и развертывание приложений.</span><span class="sxs-lookup"><span data-stu-id="eac96-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="eac96-305">Не использует локальную память.</span><span class="sxs-lookup"><span data-stu-id="eac96-305">Doesn't use local memory.</span></span>

<span data-ttu-id="eac96-306">Конфигурация распределенного кэша зависит от конкретной реализации.</span><span class="sxs-lookup"><span data-stu-id="eac96-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="eac96-307">В этой статье описывается настройка распределенных кэшей SQL Server и Redis.</span><span class="sxs-lookup"><span data-stu-id="eac96-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="eac96-308">Также доступны реализации сторонних производителей, например [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="eac96-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="eac96-309">Независимо от выбранной реализации приложение взаимодействует с кэшем с помощью <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейса.</span><span class="sxs-lookup"><span data-stu-id="eac96-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="eac96-310">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eac96-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eac96-311">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="eac96-311">Prerequisites</span></span>

<span data-ttu-id="eac96-312">Чтобы использовать SQL Server распределенный кэш, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="eac96-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="eac96-313">Чтобы использовать распределенный кэш Redis, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="eac96-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="eac96-314">Пакет Redis не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет Redis в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="eac96-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="eac96-315">Чтобы использовать распределенный кэш NCache, укажите ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) и добавьте ссылку на пакет [NCache. Microsoft. Extensions. Caching. конвертер](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="eac96-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="eac96-316">Пакет NCache не входит в `Microsoft.AspNetCore.App` пакет, поэтому необходимо отдельно ссылаться на пакет NCache в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="eac96-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="eac96-317">Интерфейс IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="eac96-317">IDistributedCache interface</span></span>

<span data-ttu-id="eac96-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Интерфейс предоставляет следующие методы для управления элементами в реализации распределенного кэша:</span><span class="sxs-lookup"><span data-stu-id="eac96-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="eac96-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Принимает строковый ключ и получает кэшированный элемент в виде `byte[]` массива, если он найден в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="eac96-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Добавляет элемент (в виде `byte[]` массива) в кэш с помощью строкового ключа.</span><span class="sxs-lookup"><span data-stu-id="eac96-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="eac96-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Обновляет элемент в кэше на основе его ключа, передавая время ожидания скользящего срока действия (если оно есть).</span><span class="sxs-lookup"><span data-stu-id="eac96-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="eac96-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Удаляет элемент кэша на основе ключа строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="eac96-323">Установка служб распределенного кэширования</span><span class="sxs-lookup"><span data-stu-id="eac96-323">Establish distributed caching services</span></span>

<span data-ttu-id="eac96-324">Зарегистрируйте реализацию <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eac96-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eac96-325">Реализации, предоставляемые платформой, описанные в этом разделе, включают:</span><span class="sxs-lookup"><span data-stu-id="eac96-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="eac96-326">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="eac96-327">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="eac96-328">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="eac96-329">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="eac96-330">Кэш распределенной памяти</span><span class="sxs-lookup"><span data-stu-id="eac96-330">Distributed Memory Cache</span></span>

<span data-ttu-id="eac96-331">Кэш распределенной памяти ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) — это предоставляемая платформой реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , которая хранит элементы в памяти.</span><span class="sxs-lookup"><span data-stu-id="eac96-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="eac96-332">Кэш распределенной памяти не является фактическим распределенным кэшем.</span><span class="sxs-lookup"><span data-stu-id="eac96-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="eac96-333">Кэшированные элементы хранятся в экземпляре приложения на сервере, где выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="eac96-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="eac96-334">Кэш распределенной памяти — это полезная реализация:</span><span class="sxs-lookup"><span data-stu-id="eac96-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="eac96-335">В сценариях разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="eac96-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="eac96-336">Если в рабочей среде используется один сервер, а использование памяти не является проблемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="eac96-337">Реализация кэша распределенной памяти абстрагирует хранилище кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="eac96-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="eac96-338">Она позволяет реализовать истинное решение распределенного кэширования в будущем, если потребуется несколько узлов или отказоустойчивость.</span><span class="sxs-lookup"><span data-stu-id="eac96-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="eac96-339">Пример приложения использует распределенный кэш памяти при запуске приложения в среде разработки в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="eac96-340">Распределенный кэш SQL Server</span><span class="sxs-lookup"><span data-stu-id="eac96-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="eac96-341">Реализация кэша распределенного SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) позволяет распределенному кэшу использовать базу данных SQL Server в качестве резервного хранилища.</span><span class="sxs-lookup"><span data-stu-id="eac96-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="eac96-342">Чтобы создать SQL Server таблицу кэшированных элементов в экземпляре SQL Server, можно использовать `sql-cache` средство.</span><span class="sxs-lookup"><span data-stu-id="eac96-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="eac96-343">Средство создает таблицу с указанными именем и схемой.</span><span class="sxs-lookup"><span data-stu-id="eac96-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="eac96-344">Создайте таблицу в SQL Server, выполнив `sql-cache create` команду.</span><span class="sxs-lookup"><span data-stu-id="eac96-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="eac96-345">Укажите SQL Server экземпляр ( `Data Source` ), базу данных ( `Initial Catalog` ), схему (например, `dbo` ) и имя таблицы (например, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="eac96-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="eac96-346">В журнал заносится сообщение, указывающее, что средство прошло успешно:</span><span class="sxs-lookup"><span data-stu-id="eac96-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="eac96-347">Таблица, созданная `sql-cache` средством, имеет следующую схему:</span><span class="sxs-lookup"><span data-stu-id="eac96-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![Таблица кэша SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="eac96-349">Приложение должно манипулировать значениями кэша с помощью экземпляра, а <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> не <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="eac96-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="eac96-350">Пример приложения реализует <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> в среде, не являющейся средой разработки, в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eac96-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="eac96-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(И, при необходимости, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> и <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) обычно хранятся вне системы управления версиями (например, хранится в [диспетчере секретов](xref:security/app-secrets) или вappsettings.jsв *appsettings.json* / *appSettings. { Файлы среды}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="eac96-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="eac96-352">Строка подключения может содержать учетные данные, которые должны содержаться в системе управления версиями.</span><span class="sxs-lookup"><span data-stu-id="eac96-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="eac96-353">Распределенный кэш Redis</span><span class="sxs-lookup"><span data-stu-id="eac96-353">Distributed Redis Cache</span></span>

<span data-ttu-id="eac96-354">[Redis](https://redis.io/) -это хранилище данных в памяти с открытым исходным кодом, которое часто используется в качестве распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="eac96-355">Вы можете использовать Redis локально, и вы можете настроить [кэш Redis для Azure](https://azure.microsoft.com/services/cache/) для приложения ASP.NET Core, размещенного в Azure.</span><span class="sxs-lookup"><span data-stu-id="eac96-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="eac96-356">Приложение настраивает реализацию кэша с помощью <xref:Microsoft.Extensions.Caching.Redis.RedisCache> экземпляра ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="eac96-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="eac96-357">Чтобы установить Redis на локальном компьютере, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="eac96-358">Установите [пакет шоколадного Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="eac96-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="eac96-359">Запустите `redis-server` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="eac96-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="eac96-360">Распределенный кэш NCache</span><span class="sxs-lookup"><span data-stu-id="eac96-360">Distributed NCache Cache</span></span>

<span data-ttu-id="eac96-361">[NCache](https://github.com/Alachisoft/NCache) — это распределенный кэш в памяти с открытым исходным кодом, разработанный изначально в .NET и .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eac96-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="eac96-362">NCache работает как локально, так и настроенный как кластер распределенного кэша для ASP.NET Core приложения, работающего в Azure или на других платформах размещения.</span><span class="sxs-lookup"><span data-stu-id="eac96-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="eac96-363">Чтобы установить и настроить NCache на локальном компьютере, ознакомьтесь с [руководством по NCache начало работы для Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="eac96-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="eac96-364">Чтобы настроить NCache, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="eac96-364">To configure NCache:</span></span>

1. <span data-ttu-id="eac96-365">Установите [NuGet с открытым исходным кодом NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="eac96-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="eac96-366">Настройте кластер кэша в [Client. нкконф](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="eac96-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="eac96-367">Добавьте следующий код в файл `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eac96-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="eac96-368">Использование распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="eac96-368">Use the distributed cache</span></span>

<span data-ttu-id="eac96-369">Чтобы использовать <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> интерфейс, запросите экземпляр <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> из любого конструктора в приложении.</span><span class="sxs-lookup"><span data-stu-id="eac96-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="eac96-370">Экземпляр предоставляется путем [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eac96-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="eac96-371">При запуске примера приложения <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> внедряется в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eac96-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="eac96-372">Текущее время кэшируется с помощью <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Дополнительные сведения см. в разделе [веб-узел: иаппликатионлифетиме Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="eac96-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="eac96-373">Пример приложения внедряет <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> в объект `IndexModel` для использования на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="eac96-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="eac96-374">При каждой загрузке страницы индекса кэш проверяется на наличие кэшированного времени в `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="eac96-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="eac96-375">Если время кэширования не истекло, отображается время.</span><span class="sxs-lookup"><span data-stu-id="eac96-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="eac96-376">Если прошло 20 секунд с момента последнего обращения к кэшированному времени (время последней загрузки этой страницы), на странице отображается *время ожидания кэширования*.</span><span class="sxs-lookup"><span data-stu-id="eac96-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="eac96-377">Немедленно обновите кэшированное время на текущее время, нажав кнопку **сбросить кэшированное** время.</span><span class="sxs-lookup"><span data-stu-id="eac96-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="eac96-378">Кнопка запускает `OnPostResetCachedTime` метод обработчика.</span><span class="sxs-lookup"><span data-stu-id="eac96-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="eac96-379">Время жизни экземпляров <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (по крайней мере, для встроенных реализаций) не обязательно должно быть ограничено одним объектом или блоком.</span><span class="sxs-lookup"><span data-stu-id="eac96-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="eac96-380">Кроме того <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , экземпляр можно создать везде, где вам может понадобиться, а не использовать di, но создание экземпляра в коде может усложнить тестирование и нарушает [принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="eac96-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="eac96-381">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="eac96-381">Recommendations</span></span>

<span data-ttu-id="eac96-382">При принятии решения о том, какая реализация <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> лучше подходит для вашего приложения, учитывайте следующее.</span><span class="sxs-lookup"><span data-stu-id="eac96-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="eac96-383">Существующая инфраструктура</span><span class="sxs-lookup"><span data-stu-id="eac96-383">Existing infrastructure</span></span>
* <span data-ttu-id="eac96-384">Требования к производительности</span><span class="sxs-lookup"><span data-stu-id="eac96-384">Performance requirements</span></span>
* <span data-ttu-id="eac96-385">Стоимость</span><span class="sxs-lookup"><span data-stu-id="eac96-385">Cost</span></span>
* <span data-ttu-id="eac96-386">Опыт работы в группе</span><span class="sxs-lookup"><span data-stu-id="eac96-386">Team experience</span></span>

<span data-ttu-id="eac96-387">Для обеспечения быстрого извлечения кэшированных данных решения кэширования обычно используют хранилище в памяти, но память является ограниченным ресурсом и может быть расширена.</span><span class="sxs-lookup"><span data-stu-id="eac96-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="eac96-388">Хранение часто используемых данных в кэше.</span><span class="sxs-lookup"><span data-stu-id="eac96-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="eac96-389">Как правило, кэш Redis обеспечивает более высокую пропускную способность и меньшую задержку, чем кэш SQL Server.</span><span class="sxs-lookup"><span data-stu-id="eac96-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="eac96-390">Однако для определения характеристик производительности стратегий кэширования обычно требуется тестирование производительности.</span><span class="sxs-lookup"><span data-stu-id="eac96-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="eac96-391">Если SQL Server используется в качестве резервного хранилища распределенного кэша, использование той же базы данных для кэша и обычного хранилища данных и извлечения приложения может негативно сказаться на производительности обоих.</span><span class="sxs-lookup"><span data-stu-id="eac96-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="eac96-392">Рекомендуется использовать выделенный экземпляр SQL Server для резервного хранилища распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="eac96-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eac96-393">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="eac96-393">Additional resources</span></span>

* [<span data-ttu-id="eac96-394">Кэш Redis в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="eac96-395">База данных SQL в Azure</span><span class="sxs-lookup"><span data-stu-id="eac96-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="eac96-396">[Поставщик ASP.NET Core IDistributedCache для NCache в веб-фермах](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache на сайте GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="eac96-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 