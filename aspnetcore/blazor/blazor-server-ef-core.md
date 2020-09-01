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
ms.openlocfilehash: b71b742c8a60b4b563649baa181b8c332ff02501
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865196"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="b092e-103">ASP.NET Core Blazor Server и Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="b092e-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="b092e-104">"По": [Джереми Ликнесс](https://github.com/JeremyLikness) (Jeremy Likness)</span><span class="sxs-lookup"><span data-stu-id="b092e-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b092e-105">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="b092e-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b092e-106">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="b092e-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b092e-107">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="b092e-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b092e-108">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b092e-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b092e-109">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b092e-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b092e-110">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="b092e-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b092e-111">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="b092e-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b092e-112">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="b092e-112">Sample app</span></span>

<span data-ttu-id="b092e-113">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="b092e-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b092e-114">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="b092e-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b092e-115">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b092e-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b092e-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b092e-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b092e-117">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="b092e-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b092e-118">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="b092e-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b092e-119">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="b092e-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b092e-120">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b092e-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b092e-121">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b092e-122">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="b092e-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b092e-123">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b092e-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="b092e-124">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="b092e-124">Database access</span></span>

<span data-ttu-id="b092e-125">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="b092e-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b092e-126">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="b092e-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b092e-127">В приложениях Blazor Server регистрация службы с заданной областью может быть проблематичной, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="b092e-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b092e-128"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="b092e-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b092e-129">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="b092e-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b092e-130">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="b092e-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b092e-131">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="b092e-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b092e-132">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="b092e-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b092e-133">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b092e-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b092e-134">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b092e-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="b092e-135">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="b092e-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b092e-136">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="b092e-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b092e-137">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="b092e-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b092e-138">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="b092e-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="b092e-139">Новые экземпляры DbContext</span><span class="sxs-lookup"><span data-stu-id="b092e-139">New DbContext instances</span></span>

<span data-ttu-id="b092e-140">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="b092e-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b092e-141">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b092e-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b092e-142">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b092e-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b092e-143">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="b092e-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b092e-144">EF Core 5.0 и более поздние версии предоставляют встроенную фабрику для создания новых контекстов.</span><span class="sxs-lookup"><span data-stu-id="b092e-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="b092e-145">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="b092e-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b092e-146">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b092e-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b092e-147">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="b092e-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b092e-148">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b092e-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="b092e-149">! [ПРИМЕЧАНИЕ.] `Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="b092e-149">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b092e-150">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="b092e-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="b092e-151">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="b092e-151">Scope to the component lifetime</span></span>

<span data-ttu-id="b092e-152">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b092e-153">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b092e-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b092e-154">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b092e-155">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="b092e-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b092e-156">Пример приложения обеспечивает удаление контакта при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-156">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b092e-157">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="b092e-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b092e-158">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="b092e-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="b092e-159">Blazor Server — это платформа приложений с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="b092e-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b092e-160">Приложение поддерживает постоянное подключение к серверу, а состояние пользователя хранится в памяти сервера в *канале*.</span><span class="sxs-lookup"><span data-stu-id="b092e-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b092e-161">Примером состояния пользователя являются данные, хранящиеся во [внедрениях зависимостей (DI)](xref:fundamentals/dependency-injection) экземпляров службы, областью действия которых является канал.</span><span class="sxs-lookup"><span data-stu-id="b092e-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b092e-162">Для уникальной модели приложения, которую предоставляет Blazor Server, требуется специальный подход к использованию Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b092e-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b092e-163">В этой статье рассматривается EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b092e-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b092e-164">Приложения Blazor WebAssembly выполняются в песочнице WebAssembly, которая запрещает большинство прямых подключений к базе данных.</span><span class="sxs-lookup"><span data-stu-id="b092e-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b092e-165">Запуск EF Core в Blazor WebAssembly выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="b092e-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b092e-166">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="b092e-166">Sample app</span></span>

<span data-ttu-id="b092e-167">Пример приложения был создан в виде справочника для приложений Blazor Server, использующих EF Core.</span><span class="sxs-lookup"><span data-stu-id="b092e-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b092e-168">Пример приложения включает сетку с операциями сортировки и фильтрации, удаления, добавления и обновления.</span><span class="sxs-lookup"><span data-stu-id="b092e-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b092e-169">В примере демонстрируется использование EF Core для обработки оптимистичного параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b092e-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b092e-170">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b092e-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b092e-171">В примере используется локальная база данных [SQLite](https://www.sqlite.org/index.html), чтобы ее можно было использовать на любой платформе.</span><span class="sxs-lookup"><span data-stu-id="b092e-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b092e-172">В этом примере также настраивается ведение журнала базы данных для отображения создаваемых запросов SQL.</span><span class="sxs-lookup"><span data-stu-id="b092e-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b092e-173">Это настраивается в `appsettings.Development.json`.</span><span class="sxs-lookup"><span data-stu-id="b092e-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b092e-174">Компоненты сетки для добавления и просмотра используют шаблон "контекст на операцию", когда контекст создается для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b092e-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b092e-175">Компонент редактирования использует шаблон "контекст на компонент", когда контекст создается для каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b092e-176">Для некоторых примеров кода в этом разделе требуются пространства имен и службы, которые не показаны.</span><span class="sxs-lookup"><span data-stu-id="b092e-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b092e-177">Для просмотра полностью работающего кода, включая обязательные директивы [`@using`](xref:mvc/views/razor#using) и [`@inject`](xref:mvc/views/razor#inject) для примеров Razor, см. [пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b092e-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="b092e-178">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="b092e-178">Database access</span></span>

<span data-ttu-id="b092e-179">EF Core опирается на <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве средства [настройки доступа к базе данных](/ef/core/miscellaneous/configuring-dbcontext) и действия в виде [*единицы работы*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="b092e-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b092e-180">EF Core предоставляет расширение <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> для приложений ASP.NET Core, которое по умолчанию регистрирует контекст как службу *с заданной областью*.</span><span class="sxs-lookup"><span data-stu-id="b092e-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b092e-181">В приложениях Blazor Server это может быть проблематично, так как экземпляр является общим для компонентов в канале пользователя.</span><span class="sxs-lookup"><span data-stu-id="b092e-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b092e-182"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не предназначен для одновременного использования.</span><span class="sxs-lookup"><span data-stu-id="b092e-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b092e-183">Существующие времена существования не подходят по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="b092e-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b092e-184">**Отдельная**. Состояние используется всеми пользователями приложения, что приводит к неприемлемому одновременному использованию.</span><span class="sxs-lookup"><span data-stu-id="b092e-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b092e-185">**С заданной областью** (по умолчанию). Приводит к той же проблеме для компонентов одного и того же пользователя.</span><span class="sxs-lookup"><span data-stu-id="b092e-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b092e-186">**Временная**. В каждом запросе создается новый экземпляр, но, поскольку компоненты могут быть длительного времени существования, это приводит к более долгоживущему контексту, чем предполагалось.</span><span class="sxs-lookup"><span data-stu-id="b092e-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="b092e-187">Доступ к базе данных</span><span class="sxs-lookup"><span data-stu-id="b092e-187">Database access</span></span>

<span data-ttu-id="b092e-188">Следующие рекомендации предназначены для обеспечения единообразного подхода к использованию EF Core в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b092e-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b092e-189">По умолчанию рассмотрите возможность использования одного контекста для каждой операции.</span><span class="sxs-lookup"><span data-stu-id="b092e-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="b092e-190">Контекст предназначен для быстрого создания экземпляров с низкими накладными расходами.</span><span class="sxs-lookup"><span data-stu-id="b092e-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b092e-191">Используйте флаг для предотвращения нескольких одновременных операций.</span><span class="sxs-lookup"><span data-stu-id="b092e-191">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b092e-192">Размещайте операции после строки `Loading = true;` в блоке `try`.</span><span class="sxs-lookup"><span data-stu-id="b092e-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b092e-193">Для длительных операций, использующих функции EF Core по [отслеживанию изменений](/ef/core/querying/tracking) или [управлению параллелизмом](/ef/core/saving/concurrency), [ограничьте область действия контекста на время существования компонента](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="b092e-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="b092e-194">Создание новых экземпляров DbContext</span><span class="sxs-lookup"><span data-stu-id="b092e-194">Create new DbContext instances</span></span>

<span data-ttu-id="b092e-195">Самый быстрый способ создать новый экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext> — использовать `new`.</span><span class="sxs-lookup"><span data-stu-id="b092e-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b092e-196">Однако существует несколько сценариев, в которых может потребоваться разрешение дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b092e-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b092e-197">Например, для настройки контекста может потребоваться [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b092e-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b092e-198">Рекомендуемым решением для создания нового экземпляра <xref:Microsoft.EntityFrameworkCore.DbContext> с зависимостями является использование фабрики.</span><span class="sxs-lookup"><span data-stu-id="b092e-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b092e-199">Пример приложения реализует собственную фабрику в `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="b092e-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="b092e-200">В предыдущей фабрике <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> удовлетворяет все зависимости через поставщика услуг.</span><span class="sxs-lookup"><span data-stu-id="b092e-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="b092e-201">В следующем примере настраивается [SQLite](https://www.sqlite.org/index.html) и включается ведение журнала данных.</span><span class="sxs-lookup"><span data-stu-id="b092e-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b092e-202">Этот код использует метод расширения для настройки фабрики баз данных для внедрения зависимостей и предоставления параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b092e-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b092e-203">Фабрика внедряется в компоненты и используется для создания новых экземпляров.</span><span class="sxs-lookup"><span data-stu-id="b092e-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b092e-204">Например, для `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b092e-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="b092e-205">! [ПРИМЕЧАНИЕ.] `Wrapper` является [ссылкой на компонент](xref:blazor/components/index#capture-references-to-components) для компонента `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="b092e-205">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b092e-206">См. компонент `Index` (`Pages/Index.razor`) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="b092e-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="b092e-207">Область действия на время существования компонента</span><span class="sxs-lookup"><span data-stu-id="b092e-207">Scope to the component lifetime</span></span>

<span data-ttu-id="b092e-208">Может потребоваться создать экземпляр <xref:Microsoft.EntityFrameworkCore.DbContext>, который будет существовать в течение времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b092e-209">Это позволяет использовать его как [единицу работы](https://martinfowler.com/eaaCatalog/unitOfWork.html) и пользоваться преимуществами встроенных функций, таких как отслеживание изменений и разрешение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="b092e-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b092e-210">Фабрику можно использовать для создания контекста и наблюдения за временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b092e-211">Сначала реализуйте <xref:System.IDisposable> и вставьте фабрику, как показано в `Pages/EditContact.razor`.</span><span class="sxs-lookup"><span data-stu-id="b092e-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b092e-212">Пример приложения обеспечивает удаление контакта при удалении компонента.</span><span class="sxs-lookup"><span data-stu-id="b092e-212">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b092e-213">Наконец, [`OnInitializedAsync`](xref:blazor/components/lifecycle) переопределяется для создания нового контекста.</span><span class="sxs-lookup"><span data-stu-id="b092e-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b092e-214">В примере приложения [`OnInitializedAsync`](xref:blazor/components/lifecycle) загружает контакт в том же методе.</span><span class="sxs-lookup"><span data-stu-id="b092e-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="b092e-215">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="b092e-215">In the preceding example:</span></span>

* <span data-ttu-id="b092e-216">Если `Busy` имеет значение `true`, могут начинаться асинхронные операции.</span><span class="sxs-lookup"><span data-stu-id="b092e-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="b092e-217">Если `Busy` возвращается в значение `false`, асинхронные операции должны быть завершены.</span><span class="sxs-lookup"><span data-stu-id="b092e-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="b092e-218">Разместите дополнительную логику обработки ошибок в блоке `catch`.</span><span class="sxs-lookup"><span data-stu-id="b092e-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b092e-219">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b092e-219">Additional resources</span></span>

> [<span data-ttu-id="b092e-220">Документация по EF Core</span><span class="sxs-lookup"><span data-stu-id="b092e-220">EF Core documentation</span></span>](/ef/)
