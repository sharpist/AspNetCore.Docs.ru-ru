---
title: ASP.NET Core Blazor Server и Entity Framework Core (EF Core)
author: JeremyLikness
description: Руководство по использованию EF Core в приложениях Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 36c0915649d9a25cf1b6b2d9ec69c3919aa7bc2c
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424169"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="b66fe-103">ASP.NET Core Blazor Server и Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="b66fe-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="b66fe-104">"По": [Джереми Ликнесс](https://github.com/JeremyLikness) (Jeremy Likness)</span><span class="sxs-lookup"><span data-stu-id="b66fe-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b66fe-105">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="b66fe-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b66fe-106">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="b66fe-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b66fe-107">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="b66fe-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b66fe-108">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b66fe-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b66fe-109">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b66fe-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b66fe-110">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="b66fe-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b66fe-111">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="b66fe-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="b66fe-112">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="b66fe-112">Sample app</span></span></h2>

<span data-ttu-id="b66fe-113">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="b66fe-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b66fe-114">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="b66fe-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b66fe-115">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b66fe-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b66fe-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b66fe-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b66fe-117">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="b66fe-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b66fe-118">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="b66fe-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b66fe-119">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b66fe-120">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b66fe-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b66fe-121">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b66fe-122">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="b66fe-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b66fe-123">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b66fe-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="b66fe-124">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="b66fe-124">Database access</span></span></h2>

<span data-ttu-id="b66fe-125">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="b66fe-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b66fe-126">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="b66fe-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b66fe-127">В приложениях Blazor Server регистрация службы с заданной областью может быть проблематичной, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="b66fe-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b66fe-128"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="b66fe-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b66fe-129">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="b66fe-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b66fe-130">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="b66fe-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b66fe-131">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="b66fe-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b66fe-132">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="b66fe-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b66fe-133">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b66fe-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b66fe-134">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b66fe-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="b66fe-135">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="b66fe-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b66fe-136">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="b66fe-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b66fe-137">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b66fe-138">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="b66fe-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="b66fe-139">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="b66fe-139">New DbContext instances</span></span></h3>

<span data-ttu-id="b66fe-140">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b66fe-141">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b66fe-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b66fe-142">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b66fe-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b66fe-143">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="b66fe-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b66fe-144">EF Core 5.0 и более поздние версии предоставляют встроенную фабрику для создания новых контекстов.</span><span class="sxs-lookup"><span data-stu-id="b66fe-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="b66fe-145">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="b66fe-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b66fe-146">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b66fe-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b66fe-147">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="b66fe-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b66fe-148">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b66fe-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b66fe-149">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b66fe-150">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="b66fe-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="b66fe-151">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="b66fe-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b66fe-152">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b66fe-153">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b66fe-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b66fe-154">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b66fe-155">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b66fe-156">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b66fe-157">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="b66fe-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b66fe-158">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="b66fe-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="b66fe-159">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="b66fe-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b66fe-160">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="b66fe-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b66fe-161">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="b66fe-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b66fe-162">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b66fe-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b66fe-163">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b66fe-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b66fe-164">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="b66fe-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b66fe-165">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="b66fe-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="b66fe-166">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="b66fe-166">Sample app</span></span></h2>

<span data-ttu-id="b66fe-167">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="b66fe-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b66fe-168">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="b66fe-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b66fe-169">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b66fe-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b66fe-170">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b66fe-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b66fe-171">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="b66fe-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b66fe-172">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="b66fe-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b66fe-173">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b66fe-174">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b66fe-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b66fe-175">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b66fe-176">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="b66fe-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b66fe-177">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b66fe-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="b66fe-178">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="b66fe-178">Database access</span></span></h2>

<span data-ttu-id="b66fe-179">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="b66fe-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b66fe-180">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="b66fe-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b66fe-181">В приложениях Blazor Server это может быть проблематично, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="b66fe-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b66fe-182"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="b66fe-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b66fe-183">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="b66fe-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b66fe-184">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="b66fe-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b66fe-185">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="b66fe-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b66fe-186">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="b66fe-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b66fe-187">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b66fe-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b66fe-188">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b66fe-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="b66fe-189">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="b66fe-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b66fe-190">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="b66fe-190">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b66fe-191">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b66fe-192">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="b66fe-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="b66fe-193">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="b66fe-193">New DbContext instances</span></span></h3>

<span data-ttu-id="b66fe-194">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b66fe-195">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b66fe-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b66fe-196">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b66fe-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b66fe-197">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="b66fe-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b66fe-198">Пример приложения реализует собственную фабрику в `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="b66fe-199">В предыдущей фабрике:</span><span class="sxs-lookup"><span data-stu-id="b66fe-199">In the preceding factory:</span></span>

* <span data-ttu-id="b66fe-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> удовлетворяет все зависимости через поставщика услуг;</span><span class="sxs-lookup"><span data-stu-id="b66fe-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="b66fe-201">`IDbContextFactory` доступен в EF Core ASP.NET Core 5.0 или более поздней версии, поэтому интерфейс [реализован в примере приложения для ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="b66fe-201">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="b66fe-202">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="b66fe-202">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b66fe-203">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b66fe-203">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b66fe-204">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="b66fe-204">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b66fe-205">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b66fe-205">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b66fe-206">`Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-206">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b66fe-207">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="b66fe-207">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="b66fe-208">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="b66fe-208">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b66fe-209">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-209">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b66fe-210">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b66fe-210">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b66fe-211">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-211">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b66fe-212">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-212">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b66fe-213">Пример приложения обеспечивает удаление контекста при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="b66fe-213">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b66fe-214">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="b66fe-214">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b66fe-215">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="b66fe-215">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="b66fe-216">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="b66fe-216">In the preceding example:</span></span>

* <span data-ttu-id="b66fe-217">Если `Busy` имеет значение `true`, могут начинаться асинхронные операции.</span><span class="sxs-lookup"><span data-stu-id="b66fe-217">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="b66fe-218">Если `Busy` возвращается в значение `false`, асинхронные операции должны быть завершены.</span><span class="sxs-lookup"><span data-stu-id="b66fe-218">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="b66fe-219">Разместите дополнительную логику обработки ошибок в блоке `catch`.</span><span class="sxs-lookup"><span data-stu-id="b66fe-219">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b66fe-220">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b66fe-220">Additional resources</span></span>

* [<span data-ttu-id="b66fe-221">Документация по EF Core</span><span class="sxs-lookup"><span data-stu-id="b66fe-221">EF Core documentation</span></span>](/ef/)
