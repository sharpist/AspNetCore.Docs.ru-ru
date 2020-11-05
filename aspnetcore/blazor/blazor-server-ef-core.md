---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: и Entity Framework Core (EF Core)'
author: JeremyLikness
description: Руководство по использованию EF Core в приложениях :::no-loc(Blazor Server):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: bfc8f334b9229fed54e6b9841e4fb255ed18249a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056625"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="4f593-103">ASP.NET Core :::no-loc(Blazor Server)::: и Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="4f593-103">ASP.NET Core :::no-loc(Blazor Server)::: with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="4f593-104">"По": [Джереми Ликнесс](https://github.com/JeremyLikness) (Jeremy Likness)</span><span class="sxs-lookup"><span data-stu-id="4f593-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4f593-105">:::no-loc(Blazor Server)::: — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="4f593-105">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="4f593-106">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="4f593-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="4f593-107">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="4f593-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="4f593-108">Для уникальной модели приложения, которую предоставляет :::no-loc(Blazor Server):::, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4f593-108">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4f593-109">В этой статье рассматривается EF Core в приложениях :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="4f593-109">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="4f593-110">Приложения :::no-loc(Blazor WebAssembly)::: выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-110">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="4f593-111">Запуск EF Core в :::no-loc(Blazor WebAssembly)::: выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="4f593-111">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="4f593-112">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="4f593-112">Sample app</span></span></h2>

<span data-ttu-id="4f593-113">Пример приложения был создан в виде справочника для приложений :::no-loc(Blazor Server):::, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="4f593-113">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="4f593-114">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="4f593-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="4f593-115">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="4f593-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="4f593-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f593-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4f593-117">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="4f593-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="4f593-118">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="4f593-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="4f593-119">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="4f593-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="4f593-120">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="4f593-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="4f593-121">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="4f593-122">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="4f593-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="4f593-123">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров :::no-loc(Razor):::, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="4f593-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="4f593-124">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="4f593-124">Database access</span></span></h2>

<span data-ttu-id="4f593-125">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="4f593-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="4f593-126">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="4f593-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="4f593-127">В приложениях :::no-loc(Blazor Server)::: регистрация службы с заданной областью может быть проблематичной, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="4f593-127">In :::no-loc(Blazor Server)::: apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="4f593-128"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="4f593-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="4f593-129">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="4f593-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="4f593-130">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="4f593-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="4f593-131">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="4f593-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="4f593-132">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="4f593-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="4f593-133">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="4f593-133">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="4f593-134">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="4f593-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="4f593-135">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="4f593-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="4f593-136">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="4f593-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="4f593-137">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="4f593-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="4f593-138">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="4f593-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="4f593-139">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="4f593-139">New DbContext instances</span></span></h3>

<span data-ttu-id="4f593-140">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="4f593-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="4f593-141">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4f593-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="4f593-142">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="4f593-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="4f593-143">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="4f593-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="4f593-144">EF Core 5.0 и более поздние версии предоставляют встроенную фабрику для создания новых контекстов.</span><span class="sxs-lookup"><span data-stu-id="4f593-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="4f593-145">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="4f593-146">В этом коде используется [метод расширения (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4f593-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="4f593-147">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="4f593-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="4f593-148">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f593-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="4f593-149">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="4f593-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="4f593-150">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="4f593-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="4f593-151">Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании [модели :::no-loc(Identity)::: ASP.NET Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="4f593-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="4f593-152">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="4f593-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="4f593-153">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="4f593-154">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="4f593-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="4f593-155">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="4f593-156">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="4f593-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="4f593-157">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="4f593-158">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="4f593-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="4f593-159">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="4f593-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="4f593-160">Включение ведения журнала для конфиденциальных данных</span><span class="sxs-lookup"><span data-stu-id="4f593-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="4f593-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы.</span><span class="sxs-lookup"><span data-stu-id="4f593-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="4f593-162">Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="4f593-163">Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно** , так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="4f593-164">Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="4f593-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="4f593-165">:::no-loc(Blazor Server)::: — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="4f593-165">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="4f593-166">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="4f593-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="4f593-167">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="4f593-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="4f593-168">Для уникальной модели приложения, которую предоставляет :::no-loc(Blazor Server):::, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4f593-168">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4f593-169">В этой статье рассматривается EF Core в приложениях :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="4f593-169">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="4f593-170">Приложения :::no-loc(Blazor WebAssembly)::: выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-170">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="4f593-171">Запуск EF Core в :::no-loc(Blazor WebAssembly)::: выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="4f593-171">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="4f593-172">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="4f593-172">Sample app</span></span></h2>

<span data-ttu-id="4f593-173">Пример приложения был создан в виде справочника для приложений :::no-loc(Blazor Server):::, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="4f593-173">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="4f593-174">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="4f593-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="4f593-175">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="4f593-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="4f593-176">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f593-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4f593-177">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="4f593-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="4f593-178">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="4f593-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="4f593-179">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="4f593-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="4f593-180">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="4f593-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="4f593-181">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="4f593-182">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="4f593-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="4f593-183">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров :::no-loc(Razor):::, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="4f593-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="4f593-184">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="4f593-184">Database access</span></span></h2>

<span data-ttu-id="4f593-185">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="4f593-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="4f593-186">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="4f593-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="4f593-187">В приложениях :::no-loc(Blazor Server)::: это может быть проблематично, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="4f593-187">In :::no-loc(Blazor Server)::: apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="4f593-188"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="4f593-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="4f593-189">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="4f593-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="4f593-190">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="4f593-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="4f593-191">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="4f593-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="4f593-192">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="4f593-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="4f593-193">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="4f593-193">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="4f593-194">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="4f593-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="4f593-195">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="4f593-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="4f593-196">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="4f593-196">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="4f593-197">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="4f593-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="4f593-198">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="4f593-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="4f593-199">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="4f593-199">New DbContext instances</span></span></h3>

<span data-ttu-id="4f593-200">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="4f593-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="4f593-201">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4f593-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="4f593-202">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="4f593-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="4f593-203">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="4f593-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="4f593-204">Пример приложения реализует собственную фабрику в `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="4f593-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="4f593-205">В предыдущей фабрике:</span><span class="sxs-lookup"><span data-stu-id="4f593-205">In the preceding factory:</span></span>

* <span data-ttu-id="4f593-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> удовлетворяет все зависимости через поставщика услуг;</span><span class="sxs-lookup"><span data-stu-id="4f593-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="4f593-207">`IDbContextFactory` доступен в EF Core ASP.NET Core 5.0 или более поздней версии, поэтому интерфейс [реализован в примере приложения для ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="4f593-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="4f593-208">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="4f593-209">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4f593-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="4f593-210">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="4f593-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="4f593-211">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f593-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="4f593-212">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="4f593-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="4f593-213">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="4f593-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="4f593-214">Новые экземпляры <xref:Microsoft.EntityFrameworkCore.DbContext> можно создать с помощью фабрики, которая позволяет настроить строку подключения для каждого экземпляра `DbContext`, например при использовании [модели :::no-loc(Identity)::: ASP.NET Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="4f593-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="4f593-215">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="4f593-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="4f593-216">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="4f593-217">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="4f593-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="4f593-218">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="4f593-219">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="4f593-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="4f593-220">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="4f593-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="4f593-221">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="4f593-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="4f593-222">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="4f593-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="4f593-223">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="4f593-223">In the preceding example:</span></span>

* <span data-ttu-id="4f593-224">Если `Busy` имеет значение `true`, могут начинаться асинхронные операции.</span><span class="sxs-lookup"><span data-stu-id="4f593-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="4f593-225">Если `Busy` возвращается в значение `false`, асинхронные операции должны быть завершены.</span><span class="sxs-lookup"><span data-stu-id="4f593-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="4f593-226">Разместите дополнительную логику обработки ошибок в блоке `catch`.</span><span class="sxs-lookup"><span data-stu-id="4f593-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="4f593-227">Включение ведения журнала для конфиденциальных данных</span><span class="sxs-lookup"><span data-stu-id="4f593-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="4f593-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> позволяет включить данные приложения в сообщения об исключениях и журналы платформы.</span><span class="sxs-lookup"><span data-stu-id="4f593-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="4f593-229">Записанные в журнал данные могут содержать значения, присвоенные свойствам экземпляров сущностей, и значения параметров для команд, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="4f593-230">Использовать <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> для записи данных в журнал **небезопасно** , так как этот метод может раскрывать пароли и другие личные сведения (PII) при записи инструкций SQL, выполняемых в базе данных.</span><span class="sxs-lookup"><span data-stu-id="4f593-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="4f593-231">Мы рекомендуем включать метод <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> только на этапах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="4f593-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4f593-232">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4f593-232">Additional resources</span></span>

* [<span data-ttu-id="4f593-233">Документация по EF Core</span><span class="sxs-lookup"><span data-stu-id="4f593-233">EF Core documentation</span></span>](/ef/)
