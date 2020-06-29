---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 34ed08a5b49b56fd37628032ac73fe03a34448e6
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240846"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="c992c-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c992c-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="c992c-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="c992c-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c992c-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="c992c-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c992c-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c992c-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c992c-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c992c-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c992c-108">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-108">Overview of dependency injection</span></span>

<span data-ttu-id="c992c-109">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="c992c-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c992c-110">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="c992c-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="c992c-111">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c992c-112">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c992c-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="c992c-113">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="c992c-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c992c-114">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c992c-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c992c-115">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="c992c-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c992c-116">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="c992c-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c992c-117">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="c992c-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c992c-118">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="c992c-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c992c-119">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="c992c-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c992c-120">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c992c-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c992c-121">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c992c-122">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c992c-123">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="c992c-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c992c-124">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c992c-125">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="c992c-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c992c-126">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="c992c-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c992c-127">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="c992c-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c992c-128">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c992c-129">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="c992c-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="c992c-130">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="c992c-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c992c-131">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c992c-132">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="c992c-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c992c-133">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="c992c-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c992c-134">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="c992c-135">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c992c-136">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="c992c-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="c992c-137">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="c992c-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="c992c-138">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c992c-139">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="c992c-140">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="c992c-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="c992c-141">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="c992c-142">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="c992c-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="c992c-143">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="c992c-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="c992c-144">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="c992c-145">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c992c-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="c992c-146">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="c992c-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="c992c-147">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="c992c-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="c992c-148">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="c992c-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="c992c-149">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="c992c-149">Services injected into Startup</span></span>

<span data-ttu-id="c992c-150">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="c992c-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c992c-151">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c992c-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c992c-152">Дополнительные сведения см. в разделе <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c992c-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c992c-153">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="c992c-153">Framework-provided services</span></span>

<span data-ttu-id="c992c-154">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c992c-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c992c-155">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="c992c-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c992c-156">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="c992c-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="c992c-157">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c992c-158">Тип службы</span><span class="sxs-lookup"><span data-stu-id="c992c-158">Service Type</span></span> | <span data-ttu-id="c992c-159">Срок действия</span><span class="sxs-lookup"><span data-stu-id="c992c-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c992c-160">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="c992c-161">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="c992c-162">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c992c-163">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c992c-164">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c992c-165">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c992c-166">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c992c-167">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c992c-168">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c992c-169">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c992c-170">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c992c-171">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c992c-172">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c992c-173">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c992c-174">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="c992c-174">Register additional services with extension methods</span></span>

<span data-ttu-id="c992c-175">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="c992c-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="c992c-176">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="c992c-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="c992c-177">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="c992c-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="c992c-178">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="c992c-178">Service lifetimes</span></span>

<span data-ttu-id="c992c-179">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c992c-180">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="c992c-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c992c-181">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-181">Transient</span></span>

<span data-ttu-id="c992c-182">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c992c-183">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c992c-184">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c992c-185">Область действия</span><span class="sxs-lookup"><span data-stu-id="c992c-185">Scoped</span></span>

<span data-ttu-id="c992c-186">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="c992c-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c992c-187">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="c992c-188">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c992c-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c992c-189">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="c992c-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c992c-190">Дополнительные сведения см. в разделе <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c992c-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c992c-191">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-191">Singleton</span></span>

<span data-ttu-id="c992c-192">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="c992c-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="c992c-193">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="c992c-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c992c-194">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="c992c-195">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="c992c-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="c992c-196">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="c992c-197">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="c992c-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c992c-198">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c992c-199">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="c992c-199">Service registration methods</span></span>

<span data-ttu-id="c992c-200">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="c992c-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="c992c-201">Метод</span><span class="sxs-lookup"><span data-stu-id="c992c-201">Method</span></span> | <span data-ttu-id="c992c-202">Автоматически</span><span class="sxs-lookup"><span data-stu-id="c992c-202">Automatic</span></span><br><span data-ttu-id="c992c-203">объект</span><span class="sxs-lookup"><span data-stu-id="c992c-203">object</span></span><br><span data-ttu-id="c992c-204">удаление</span><span class="sxs-lookup"><span data-stu-id="c992c-204">disposal</span></span> | <span data-ttu-id="c992c-205">Несколько</span><span class="sxs-lookup"><span data-stu-id="c992c-205">Multiple</span></span><br><span data-ttu-id="c992c-206">реализации</span><span class="sxs-lookup"><span data-stu-id="c992c-206">implementations</span></span> | <span data-ttu-id="c992c-207">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="c992c-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c992c-208">Пример</span><span class="sxs-lookup"><span data-stu-id="c992c-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c992c-209">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-209">Yes</span></span> | <span data-ttu-id="c992c-210">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-210">Yes</span></span> | <span data-ttu-id="c992c-211">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-212">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="c992c-213">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-213">Yes</span></span> | <span data-ttu-id="c992c-214">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-214">Yes</span></span> | <span data-ttu-id="c992c-215">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c992c-216">Пример</span><span class="sxs-lookup"><span data-stu-id="c992c-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c992c-217">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-217">Yes</span></span> | <span data-ttu-id="c992c-218">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-218">No</span></span> | <span data-ttu-id="c992c-219">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-220">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="c992c-221">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-221">No</span></span> | <span data-ttu-id="c992c-222">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-222">Yes</span></span> | <span data-ttu-id="c992c-223">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-224">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="c992c-225">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-225">No</span></span> | <span data-ttu-id="c992c-226">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-226">No</span></span> | <span data-ttu-id="c992c-227">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-227">Yes</span></span> |

<span data-ttu-id="c992c-228">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="c992c-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c992c-229">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="c992c-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c992c-230">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="c992c-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c992c-231">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c992c-232">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="c992c-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c992c-233">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="c992c-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c992c-234">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="c992c-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c992c-235">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="c992c-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c992c-236">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="c992c-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c992c-237">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="c992c-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="c992c-238">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="c992c-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c992c-239">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="c992c-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c992c-240">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="c992c-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c992c-241">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="c992c-241">Constructor injection behavior</span></span>

<span data-ttu-id="c992c-242">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="c992c-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c992c-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="c992c-244">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="c992c-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="c992c-245">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c992c-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c992c-246">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="c992c-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c992c-247">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="c992c-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c992c-248">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c992c-249">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c992c-249">Entity Framework contexts</span></span>

<span data-ttu-id="c992c-250">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c992c-251">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c992c-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c992c-252">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c992c-253">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="c992c-253">Lifetime and registration options</span></span>

<span data-ttu-id="c992c-254">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="c992c-255">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c992c-256">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c992c-257">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="c992c-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="c992c-258">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="c992c-259">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="c992c-260">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="c992c-261">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="c992c-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="c992c-262">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="c992c-263">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="c992c-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="c992c-264">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="c992c-265">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="c992c-266">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="c992c-267">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="c992c-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="c992c-268">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="c992c-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="c992c-269">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="c992c-270">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="c992c-271">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="c992c-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="c992c-272">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="c992c-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="c992c-273">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="c992c-273">**First request:**</span></span>

<span data-ttu-id="c992c-274">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="c992c-274">Controller operations:</span></span>

<span data-ttu-id="c992c-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="c992c-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="c992c-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c992c-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c992c-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-278">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-279">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c992c-279">`OperationService` operations:</span></span>

<span data-ttu-id="c992c-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="c992c-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="c992c-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c992c-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c992c-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-283">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-284">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="c992c-284">**Second request:**</span></span>

<span data-ttu-id="c992c-285">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="c992c-285">Controller operations:</span></span>

<span data-ttu-id="c992c-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="c992c-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="c992c-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c992c-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c992c-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-289">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-290">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c992c-290">`OperationService` operations:</span></span>

<span data-ttu-id="c992c-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="c992c-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="c992c-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c992c-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c992c-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-294">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-295">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="c992c-296">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="c992c-296">*Transient* objects are always different.</span></span> <span data-ttu-id="c992c-297">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="c992c-298">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c992c-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="c992c-299">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="c992c-300">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="c992c-301">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="c992c-301">Call services from main</span></span>

<span data-ttu-id="c992c-302">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c992c-303">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="c992c-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="c992c-304">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c992c-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="c992c-305">Проверка области</span><span class="sxs-lookup"><span data-stu-id="c992c-305">Scope validation</span></span>

<span data-ttu-id="c992c-306">Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="c992c-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c992c-307">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="c992c-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c992c-308">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="c992c-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c992c-309">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="c992c-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c992c-310">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="c992c-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c992c-311">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="c992c-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c992c-312">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="c992c-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c992c-313">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="c992c-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c992c-314">Дополнительные сведения см. в разделе <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="c992c-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="c992c-315">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="c992c-315">Request Services</span></span>

<span data-ttu-id="c992c-316">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="c992c-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c992c-317">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c992c-318">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c992c-319">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="c992c-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c992c-320">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="c992c-321">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="c992c-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="c992c-322">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c992c-323">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-323">Design services for dependency injection</span></span>

<span data-ttu-id="c992c-324">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="c992c-324">Best practices are to:</span></span>

* <span data-ttu-id="c992c-325">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c992c-326">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c992c-327">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c992c-328">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c992c-329">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="c992c-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c992c-330">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="c992c-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c992c-331">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c992c-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c992c-332">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="c992c-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c992c-333">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="c992c-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="c992c-334">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="c992c-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c992c-335">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="c992c-335">Disposal of services</span></span>

<span data-ttu-id="c992c-336">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c992c-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c992c-337">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="c992c-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="c992c-338">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="c992c-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="c992c-339">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="c992c-339">In the following example:</span></span>

* <span data-ttu-id="c992c-340">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c992c-341">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c992c-342">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="c992c-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c992c-343">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c992c-344">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="c992c-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c992c-345">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="c992c-345">Transient, limited lifetime</span></span>

<span data-ttu-id="c992c-346">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="c992c-346">**Scenario**</span></span>

<span data-ttu-id="c992c-347">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="c992c-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c992c-348">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="c992c-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="c992c-349">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="c992c-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c992c-350">**Решение**</span><span class="sxs-lookup"><span data-stu-id="c992c-350">**Solution**</span></span>

<span data-ttu-id="c992c-351">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="c992c-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c992c-352">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="c992c-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c992c-353">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="c992c-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c992c-354">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="c992c-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c992c-355">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c992c-356">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="c992c-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c992c-357">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="c992c-357">**Scenario**</span></span>

<span data-ttu-id="c992c-358">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c992c-359">**Решение**</span><span class="sxs-lookup"><span data-stu-id="c992c-359">**Solution**</span></span>

<span data-ttu-id="c992c-360">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="c992c-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c992c-361">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="c992c-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c992c-362">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c992c-363">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="c992c-364">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="c992c-364">General Guidelines</span></span>

* <span data-ttu-id="c992c-365">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="c992c-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c992c-366">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="c992c-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c992c-367">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="c992c-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c992c-368">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="c992c-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c992c-369">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c992c-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c992c-370">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c992c-371">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c992c-372">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="c992c-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c992c-373">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="c992c-373">Default service container replacement</span></span>

<span data-ttu-id="c992c-374">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="c992c-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c992c-375">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="c992c-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c992c-376">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="c992c-376">Property injection</span></span>
* <span data-ttu-id="c992c-377">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="c992c-377">Injection based on name</span></span>
* <span data-ttu-id="c992c-378">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="c992c-378">Child containers</span></span>
* <span data-ttu-id="c992c-379">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="c992c-379">Custom lifetime management</span></span>
* <span data-ttu-id="c992c-380">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="c992c-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c992c-381">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="c992c-381">Convention-based registration</span></span>

<span data-ttu-id="c992c-382">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="c992c-383">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="c992c-383">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="c992c-384">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-384">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="c992c-385">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="c992c-385">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="c992c-386">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-386">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="c992c-387">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="c992c-387">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="c992c-388">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-388">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="c992c-389">Unity</span><span class="sxs-lookup"><span data-stu-id="c992c-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c992c-390">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="c992c-390">Thread safety</span></span>

<span data-ttu-id="c992c-391">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="c992c-392">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="c992c-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c992c-393">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="c992c-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c992c-394">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="c992c-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c992c-395">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="c992c-395">Recommendations</span></span>

* <span data-ttu-id="c992c-396">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="c992c-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c992c-397">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="c992c-398">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c992c-399">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c992c-400">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c992c-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c992c-401">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="c992c-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c992c-402">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="c992c-403">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="c992c-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="c992c-404">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="c992c-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="c992c-405">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="c992c-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="c992c-406">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="c992c-406">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="c992c-407">**Правильное.**</span><span class="sxs-lookup"><span data-stu-id="c992c-407">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="c992c-408">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c992c-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="c992c-409">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="c992c-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="c992c-410">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c992c-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="c992c-411">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="c992c-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c992c-412">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="c992c-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c992c-413">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="c992c-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c992c-414">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="c992c-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="c992c-415">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="c992c-416">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="c992c-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="c992c-417">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="c992c-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="c992c-418">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="c992c-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c992c-419">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c992c-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c992c-420">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c992c-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c992c-421">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c992c-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="c992c-422">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-422">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c992c-423">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="c992c-423">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="c992c-424">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c992c-424">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c992c-425">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="c992c-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c992c-426">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c992c-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c992c-427">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c992c-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c992c-428">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-428">Overview of dependency injection</span></span>

<span data-ttu-id="c992c-429">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="c992c-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c992c-430">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="c992c-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="c992c-431">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c992c-432">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c992c-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="c992c-433">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="c992c-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c992c-434">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="c992c-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c992c-435">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="c992c-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c992c-436">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="c992c-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c992c-437">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="c992c-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c992c-438">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="c992c-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c992c-439">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="c992c-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c992c-440">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c992c-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c992c-441">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c992c-442">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c992c-443">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="c992c-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c992c-444">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c992c-445">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="c992c-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c992c-446">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="c992c-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c992c-447">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="c992c-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c992c-448">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c992c-449">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="c992c-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="c992c-450">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="c992c-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c992c-451">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c992c-452">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="c992c-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c992c-453">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="c992c-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c992c-454">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="c992c-455">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c992c-456">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="c992c-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="c992c-457">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="c992c-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="c992c-458">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c992c-459">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="c992c-460">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="c992c-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="c992c-461">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="c992c-462">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="c992c-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="c992c-463">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="c992c-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="c992c-464">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="c992c-465">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c992c-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="c992c-466">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="c992c-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="c992c-467">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="c992c-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="c992c-468">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="c992c-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="c992c-469">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="c992c-469">Services injected into Startup</span></span>

<span data-ttu-id="c992c-470">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="c992c-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c992c-471">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c992c-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c992c-472">Дополнительные сведения см. в разделе <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c992c-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c992c-473">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="c992c-473">Framework-provided services</span></span>

<span data-ttu-id="c992c-474">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c992c-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c992c-475">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="c992c-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c992c-476">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="c992c-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="c992c-477">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c992c-478">Тип службы</span><span class="sxs-lookup"><span data-stu-id="c992c-478">Service Type</span></span> | <span data-ttu-id="c992c-479">Срок действия</span><span class="sxs-lookup"><span data-stu-id="c992c-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c992c-480">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="c992c-481">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="c992c-482">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c992c-483">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c992c-484">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c992c-485">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c992c-486">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c992c-487">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c992c-488">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c992c-489">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c992c-490">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c992c-491">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c992c-492">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c992c-493">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c992c-494">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="c992c-494">Register additional services with extension methods</span></span>

<span data-ttu-id="c992c-495">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="c992c-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="c992c-496">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="c992c-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="c992c-497">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="c992c-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="c992c-498">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="c992c-498">Service lifetimes</span></span>

<span data-ttu-id="c992c-499">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c992c-500">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="c992c-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c992c-501">Промежуточный</span><span class="sxs-lookup"><span data-stu-id="c992c-501">Transient</span></span>

<span data-ttu-id="c992c-502">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c992c-503">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c992c-504">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c992c-505">Область действия</span><span class="sxs-lookup"><span data-stu-id="c992c-505">Scoped</span></span>

<span data-ttu-id="c992c-506">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="c992c-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c992c-507">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="c992c-508">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c992c-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c992c-509">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="c992c-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c992c-510">Дополнительные сведения см. в разделе <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c992c-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c992c-511">Отдельная</span><span class="sxs-lookup"><span data-stu-id="c992c-511">Singleton</span></span>

<span data-ttu-id="c992c-512">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="c992c-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="c992c-513">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="c992c-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c992c-514">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="c992c-515">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="c992c-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="c992c-516">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="c992c-517">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="c992c-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c992c-518">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c992c-519">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="c992c-519">Service registration methods</span></span>

<span data-ttu-id="c992c-520">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="c992c-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="c992c-521">Метод</span><span class="sxs-lookup"><span data-stu-id="c992c-521">Method</span></span> | <span data-ttu-id="c992c-522">Автоматически</span><span class="sxs-lookup"><span data-stu-id="c992c-522">Automatic</span></span><br><span data-ttu-id="c992c-523">объект</span><span class="sxs-lookup"><span data-stu-id="c992c-523">object</span></span><br><span data-ttu-id="c992c-524">удаление</span><span class="sxs-lookup"><span data-stu-id="c992c-524">disposal</span></span> | <span data-ttu-id="c992c-525">Несколько</span><span class="sxs-lookup"><span data-stu-id="c992c-525">Multiple</span></span><br><span data-ttu-id="c992c-526">реализации</span><span class="sxs-lookup"><span data-stu-id="c992c-526">implementations</span></span> | <span data-ttu-id="c992c-527">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="c992c-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c992c-528">Пример</span><span class="sxs-lookup"><span data-stu-id="c992c-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c992c-529">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-529">Yes</span></span> | <span data-ttu-id="c992c-530">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-530">Yes</span></span> | <span data-ttu-id="c992c-531">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-532">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="c992c-533">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-533">Yes</span></span> | <span data-ttu-id="c992c-534">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-534">Yes</span></span> | <span data-ttu-id="c992c-535">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c992c-536">Пример</span><span class="sxs-lookup"><span data-stu-id="c992c-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c992c-537">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-537">Yes</span></span> | <span data-ttu-id="c992c-538">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-538">No</span></span> | <span data-ttu-id="c992c-539">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-540">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="c992c-541">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-541">No</span></span> | <span data-ttu-id="c992c-542">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-542">Yes</span></span> | <span data-ttu-id="c992c-543">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c992c-544">Примеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="c992c-545">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-545">No</span></span> | <span data-ttu-id="c992c-546">Нет</span><span class="sxs-lookup"><span data-stu-id="c992c-546">No</span></span> | <span data-ttu-id="c992c-547">Да</span><span class="sxs-lookup"><span data-stu-id="c992c-547">Yes</span></span> |

<span data-ttu-id="c992c-548">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="c992c-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c992c-549">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="c992c-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c992c-550">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="c992c-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c992c-551">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c992c-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c992c-552">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="c992c-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c992c-553">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="c992c-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c992c-554">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="c992c-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c992c-555">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="c992c-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c992c-556">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="c992c-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c992c-557">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="c992c-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="c992c-558">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="c992c-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c992c-559">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="c992c-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c992c-560">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="c992c-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c992c-561">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="c992c-561">Constructor injection behavior</span></span>

<span data-ttu-id="c992c-562">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="c992c-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c992c-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="c992c-564">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="c992c-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="c992c-565">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c992c-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c992c-566">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="c992c-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c992c-567">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="c992c-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c992c-568">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c992c-569">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c992c-569">Entity Framework contexts</span></span>

<span data-ttu-id="c992c-570">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="c992c-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c992c-571">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="c992c-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c992c-572">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c992c-573">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="c992c-573">Lifetime and registration options</span></span>

<span data-ttu-id="c992c-574">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="c992c-575">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c992c-576">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c992c-577">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="c992c-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="c992c-578">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="c992c-579">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="c992c-580">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="c992c-581">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="c992c-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="c992c-582">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="c992c-583">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="c992c-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="c992c-584">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c992c-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="c992c-585">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="c992c-586">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="c992c-587">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="c992c-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="c992c-588">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="c992c-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="c992c-589">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="c992c-590">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="c992c-591">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="c992c-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="c992c-592">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="c992c-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="c992c-593">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="c992c-593">**First request:**</span></span>

<span data-ttu-id="c992c-594">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="c992c-594">Controller operations:</span></span>

<span data-ttu-id="c992c-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="c992c-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="c992c-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c992c-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c992c-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-598">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-599">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c992c-599">`OperationService` operations:</span></span>

<span data-ttu-id="c992c-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="c992c-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="c992c-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c992c-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c992c-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-603">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-604">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="c992c-604">**Second request:**</span></span>

<span data-ttu-id="c992c-605">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="c992c-605">Controller operations:</span></span>

<span data-ttu-id="c992c-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="c992c-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="c992c-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c992c-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c992c-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-609">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-610">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c992c-610">`OperationService` operations:</span></span>

<span data-ttu-id="c992c-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="c992c-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="c992c-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c992c-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c992c-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c992c-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c992c-614">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c992c-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c992c-615">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="c992c-616">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="c992c-616">*Transient* objects are always different.</span></span> <span data-ttu-id="c992c-617">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c992c-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="c992c-618">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="c992c-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="c992c-619">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="c992c-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="c992c-620">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c992c-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="c992c-621">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="c992c-621">Call services from main</span></span>

<span data-ttu-id="c992c-622">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c992c-623">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="c992c-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="c992c-624">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c992c-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="c992c-625">Проверка области</span><span class="sxs-lookup"><span data-stu-id="c992c-625">Scope validation</span></span>

<span data-ttu-id="c992c-626">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="c992c-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c992c-627">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="c992c-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c992c-628">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="c992c-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c992c-629">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="c992c-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c992c-630">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="c992c-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c992c-631">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="c992c-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c992c-632">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="c992c-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c992c-633">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="c992c-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c992c-634">Дополнительные сведения см. в разделе <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="c992c-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="c992c-635">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="c992c-635">Request Services</span></span>

<span data-ttu-id="c992c-636">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="c992c-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c992c-637">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c992c-638">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c992c-639">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="c992c-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c992c-640">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="c992c-641">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="c992c-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="c992c-642">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="c992c-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c992c-643">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-643">Design services for dependency injection</span></span>

<span data-ttu-id="c992c-644">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="c992c-644">Best practices are to:</span></span>

* <span data-ttu-id="c992c-645">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c992c-646">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c992c-647">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="c992c-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c992c-648">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c992c-649">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="c992c-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c992c-650">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="c992c-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c992c-651">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c992c-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c992c-652">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="c992c-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c992c-653">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="c992c-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="c992c-654">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="c992c-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c992c-655">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="c992c-655">Disposal of services</span></span>

<span data-ttu-id="c992c-656">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c992c-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c992c-657">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="c992c-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="c992c-658">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="c992c-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="c992c-659">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="c992c-659">In the following example:</span></span>

* <span data-ttu-id="c992c-660">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c992c-661">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c992c-662">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="c992c-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c992c-663">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="c992c-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c992c-664">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="c992c-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c992c-665">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="c992c-665">Transient, limited lifetime</span></span>

<span data-ttu-id="c992c-666">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="c992c-666">**Scenario**</span></span>

<span data-ttu-id="c992c-667">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="c992c-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c992c-668">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="c992c-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="c992c-669">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="c992c-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c992c-670">**Решение**</span><span class="sxs-lookup"><span data-stu-id="c992c-670">**Solution**</span></span>

<span data-ttu-id="c992c-671">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="c992c-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c992c-672">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="c992c-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c992c-673">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="c992c-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c992c-674">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="c992c-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c992c-675">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c992c-676">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="c992c-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c992c-677">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="c992c-677">**Scenario**</span></span>

<span data-ttu-id="c992c-678">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c992c-679">**Решение**</span><span class="sxs-lookup"><span data-stu-id="c992c-679">**Solution**</span></span>

<span data-ttu-id="c992c-680">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="c992c-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c992c-681">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="c992c-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c992c-682">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c992c-683">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="c992c-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="c992c-684">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="c992c-684">General Guidelines</span></span>

* <span data-ttu-id="c992c-685">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="c992c-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c992c-686">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="c992c-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c992c-687">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="c992c-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c992c-688">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="c992c-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c992c-689">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c992c-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c992c-690">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="c992c-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c992c-691">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c992c-692">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="c992c-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c992c-693">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="c992c-693">Default service container replacement</span></span>

<span data-ttu-id="c992c-694">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="c992c-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c992c-695">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="c992c-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c992c-696">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="c992c-696">Property injection</span></span>
* <span data-ttu-id="c992c-697">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="c992c-697">Injection based on name</span></span>
* <span data-ttu-id="c992c-698">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="c992c-698">Child containers</span></span>
* <span data-ttu-id="c992c-699">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="c992c-699">Custom lifetime management</span></span>
* <span data-ttu-id="c992c-700">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="c992c-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c992c-701">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="c992c-701">Convention-based registration</span></span>

<span data-ttu-id="c992c-702">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="c992c-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="c992c-703">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="c992c-703">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="c992c-704">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-704">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="c992c-705">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="c992c-705">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="c992c-706">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-706">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="c992c-707">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="c992c-707">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="c992c-708">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="c992c-708">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="c992c-709">Unity</span><span class="sxs-lookup"><span data-stu-id="c992c-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c992c-710">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="c992c-710">Thread safety</span></span>

<span data-ttu-id="c992c-711">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="c992c-712">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="c992c-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c992c-713">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="c992c-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c992c-714">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="c992c-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c992c-715">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="c992c-715">Recommendations</span></span>

* <span data-ttu-id="c992c-716">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="c992c-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c992c-717">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="c992c-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="c992c-718">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c992c-719">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="c992c-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c992c-720">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c992c-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c992c-721">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="c992c-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c992c-722">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c992c-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="c992c-723">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="c992c-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="c992c-724">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="c992c-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="c992c-725">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="c992c-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="c992c-726">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="c992c-726">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="c992c-727">**Правильное.**</span><span class="sxs-lookup"><span data-stu-id="c992c-727">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="c992c-728">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="c992c-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="c992c-729">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c992c-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="c992c-730">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="c992c-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c992c-731">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="c992c-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c992c-732">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="c992c-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c992c-733">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="c992c-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c992c-734">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c992c-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c992c-735">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c992c-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c992c-736">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c992c-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="c992c-737">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="c992c-737">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c992c-738">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="c992c-738">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="c992c-739">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c992c-739">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
