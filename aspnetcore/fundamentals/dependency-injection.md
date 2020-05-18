---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003181"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="ac180-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac180-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="ac180-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Скотт Эдди](https://scottaddie.com) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="ac180-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac180-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="ac180-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ac180-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ac180-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ac180-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac180-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ac180-108">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-108">Overview of dependency injection</span></span>

<span data-ttu-id="ac180-109">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="ac180-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="ac180-110">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="ac180-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="ac180-111">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ac180-112">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="ac180-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ac180-113">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="ac180-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ac180-114">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="ac180-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ac180-115">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="ac180-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="ac180-116">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="ac180-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="ac180-117">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="ac180-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ac180-118">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="ac180-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ac180-119">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="ac180-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ac180-120">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ac180-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ac180-121">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ac180-122">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ac180-123">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="ac180-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ac180-124">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ac180-125">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="ac180-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ac180-126">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="ac180-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ac180-127">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="ac180-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ac180-128">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ac180-129">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="ac180-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ac180-130">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="ac180-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ac180-131">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="ac180-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ac180-132">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="ac180-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ac180-133">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="ac180-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="ac180-134">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ac180-135">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac180-136">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="ac180-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="ac180-137">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="ac180-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ac180-138">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ac180-139">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="ac180-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="ac180-140">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="ac180-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ac180-141">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ac180-142">Например, `services.AddMvc()` добавляет службы, которые нужны для Razor Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="ac180-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="ac180-143">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="ac180-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="ac180-144">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ac180-145">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac180-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="ac180-146">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="ac180-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="ac180-147">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="ac180-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="ac180-148">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="ac180-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ac180-149">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="ac180-149">Services injected into Startup</span></span>

<span data-ttu-id="ac180-150">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="ac180-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ac180-151">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ac180-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ac180-152">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac180-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ac180-153">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="ac180-153">Framework-provided services</span></span>

<span data-ttu-id="ac180-154">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ac180-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ac180-155">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="ac180-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ac180-156">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="ac180-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="ac180-157">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="ac180-158">Тип службы</span><span class="sxs-lookup"><span data-stu-id="ac180-158">Service Type</span></span> | <span data-ttu-id="ac180-159">Время существования</span><span class="sxs-lookup"><span data-stu-id="ac180-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ac180-160">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="ac180-161">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="ac180-162">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ac180-163">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ac180-164">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ac180-165">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ac180-166">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ac180-167">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ac180-168">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ac180-169">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ac180-170">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ac180-171">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ac180-172">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ac180-173">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ac180-174">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="ac180-174">Register additional services with extension methods</span></span>

<span data-ttu-id="ac180-175">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="ac180-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="ac180-176">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="ac180-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="ac180-177">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="ac180-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ac180-178">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="ac180-178">Service lifetimes</span></span>

<span data-ttu-id="ac180-179">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="ac180-180">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="ac180-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="ac180-181">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-181">Transient</span></span>

<span data-ttu-id="ac180-182">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ac180-183">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="ac180-184">Область действия</span><span class="sxs-lookup"><span data-stu-id="ac180-184">Scoped</span></span>

<span data-ttu-id="ac180-185">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="ac180-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="ac180-186">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac180-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ac180-187">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="ac180-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ac180-188">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ac180-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ac180-189">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-189">Singleton</span></span>

<span data-ttu-id="ac180-190">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="ac180-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="ac180-191">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="ac180-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ac180-192">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="ac180-193">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="ac180-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="ac180-194">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="ac180-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ac180-195">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ac180-196">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="ac180-196">Service registration methods</span></span>

<span data-ttu-id="ac180-197">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="ac180-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="ac180-198">Метод</span><span class="sxs-lookup"><span data-stu-id="ac180-198">Method</span></span> | <span data-ttu-id="ac180-199">Автоматический</span><span class="sxs-lookup"><span data-stu-id="ac180-199">Automatic</span></span><br><span data-ttu-id="ac180-200">object</span><span class="sxs-lookup"><span data-stu-id="ac180-200">object</span></span><br><span data-ttu-id="ac180-201">удаление</span><span class="sxs-lookup"><span data-stu-id="ac180-201">disposal</span></span> | <span data-ttu-id="ac180-202">Несколько</span><span class="sxs-lookup"><span data-stu-id="ac180-202">Multiple</span></span><br><span data-ttu-id="ac180-203">реализации</span><span class="sxs-lookup"><span data-stu-id="ac180-203">implementations</span></span> | <span data-ttu-id="ac180-204">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="ac180-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ac180-205">Пример.</span><span class="sxs-lookup"><span data-stu-id="ac180-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ac180-206">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-206">Yes</span></span> | <span data-ttu-id="ac180-207">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-207">Yes</span></span> | <span data-ttu-id="ac180-208">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-209">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ac180-210">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-210">Yes</span></span> | <span data-ttu-id="ac180-211">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-211">Yes</span></span> | <span data-ttu-id="ac180-212">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ac180-213">Пример.</span><span class="sxs-lookup"><span data-stu-id="ac180-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ac180-214">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-214">Yes</span></span> | <span data-ttu-id="ac180-215">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-215">No</span></span> | <span data-ttu-id="ac180-216">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-217">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ac180-218">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-218">No</span></span> | <span data-ttu-id="ac180-219">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-219">Yes</span></span> | <span data-ttu-id="ac180-220">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-221">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ac180-222">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-222">No</span></span> | <span data-ttu-id="ac180-223">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-223">No</span></span> | <span data-ttu-id="ac180-224">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-224">Yes</span></span> |

<span data-ttu-id="ac180-225">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="ac180-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ac180-226">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ac180-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ac180-227">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="ac180-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ac180-228">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="ac180-229">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="ac180-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ac180-230">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="ac180-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ac180-231">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="ac180-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ac180-232">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="ac180-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ac180-233">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="ac180-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ac180-234">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="ac180-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="ac180-235">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="ac180-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ac180-236">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="ac180-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="ac180-237">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="ac180-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ac180-238">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="ac180-238">Constructor injection behavior</span></span>

<span data-ttu-id="ac180-239">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="ac180-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ac180-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="ac180-241">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="ac180-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ac180-242">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ac180-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ac180-243">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="ac180-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ac180-244">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="ac180-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ac180-245">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ac180-246">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ac180-246">Entity Framework contexts</span></span>

<span data-ttu-id="ac180-247">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="ac180-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ac180-248">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="ac180-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ac180-249">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ac180-250">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="ac180-250">Lifetime and registration options</span></span>

<span data-ttu-id="ac180-251">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="ac180-252">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ac180-253">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ac180-254">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="ac180-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ac180-255">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ac180-256">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="ac180-257">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="ac180-258">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="ac180-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="ac180-259">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="ac180-260">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="ac180-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="ac180-261">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="ac180-262">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ac180-263">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ac180-264">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ac180-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="ac180-265">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="ac180-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ac180-266">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="ac180-267">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="ac180-268">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="ac180-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ac180-269">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="ac180-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="ac180-270">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="ac180-270">**First request:**</span></span>

<span data-ttu-id="ac180-271">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="ac180-271">Controller operations:</span></span>

<span data-ttu-id="ac180-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ac180-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ac180-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac180-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac180-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-276">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac180-276">`OperationService` operations:</span></span>

<span data-ttu-id="ac180-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ac180-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="ac180-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac180-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac180-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-281">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="ac180-281">**Second request:**</span></span>

<span data-ttu-id="ac180-282">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="ac180-282">Controller operations:</span></span>

<span data-ttu-id="ac180-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ac180-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ac180-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac180-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac180-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-287">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac180-287">`OperationService` operations:</span></span>

<span data-ttu-id="ac180-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ac180-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="ac180-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac180-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac180-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-292">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="ac180-293">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="ac180-293">*Transient* objects are always different.</span></span> <span data-ttu-id="ac180-294">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ac180-295">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="ac180-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="ac180-296">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="ac180-297">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ac180-298">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="ac180-298">Call services from main</span></span>

<span data-ttu-id="ac180-299">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="ac180-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ac180-300">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="ac180-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="ac180-301">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac180-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ac180-302">Проверка области</span><span class="sxs-lookup"><span data-stu-id="ac180-302">Scope validation</span></span>

<span data-ttu-id="ac180-303">Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="ac180-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ac180-304">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="ac180-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ac180-305">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="ac180-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ac180-306">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="ac180-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="ac180-307">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="ac180-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ac180-308">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="ac180-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ac180-309">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="ac180-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ac180-310">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="ac180-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ac180-311">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="ac180-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="ac180-312">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="ac180-312">Request Services</span></span>

<span data-ttu-id="ac180-313">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="ac180-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="ac180-314">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="ac180-315">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ac180-316">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="ac180-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="ac180-317">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="ac180-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="ac180-318">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="ac180-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="ac180-319">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ac180-320">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-320">Design services for dependency injection</span></span>

<span data-ttu-id="ac180-321">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="ac180-321">Best practices are to:</span></span>

* <span data-ttu-id="ac180-322">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="ac180-323">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ac180-324">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="ac180-325">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ac180-326">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="ac180-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ac180-327">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="ac180-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ac180-328">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ac180-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ac180-329">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="ac180-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="ac180-330">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="ac180-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="ac180-331">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="ac180-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ac180-332">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="ac180-332">Disposal of services</span></span>

<span data-ttu-id="ac180-333">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="ac180-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ac180-334">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="ac180-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="ac180-335">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="ac180-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="ac180-336">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ac180-336">In the following example:</span></span>

* <span data-ttu-id="ac180-337">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ac180-338">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="ac180-339">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="ac180-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ac180-340">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ac180-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="ac180-341">Сведения о вариантах удаления служб см. в разделе [Четыре способа удаления IDisposable в ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="ac180-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ac180-342">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="ac180-342">Default service container replacement</span></span>

<span data-ttu-id="ac180-343">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="ac180-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ac180-344">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="ac180-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="ac180-345">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="ac180-345">Property injection</span></span>
* <span data-ttu-id="ac180-346">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="ac180-346">Injection based on name</span></span>
* <span data-ttu-id="ac180-347">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="ac180-347">Child containers</span></span>
* <span data-ttu-id="ac180-348">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="ac180-348">Custom lifetime management</span></span>
* <span data-ttu-id="ac180-349">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="ac180-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ac180-350">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="ac180-350">Convention-based registration</span></span>

<span data-ttu-id="ac180-351">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="ac180-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="ac180-352">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="ac180-352">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="ac180-353">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-353">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="ac180-354">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="ac180-354">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="ac180-355">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-355">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="ac180-356">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="ac180-356">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="ac180-357">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-357">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="ac180-358">Unity</span><span class="sxs-lookup"><span data-stu-id="ac180-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="ac180-359">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="ac180-359">Thread safety</span></span>

<span data-ttu-id="ac180-360">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="ac180-361">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="ac180-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="ac180-362">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="ac180-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ac180-363">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="ac180-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ac180-364">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="ac180-364">Recommendations</span></span>

* <span data-ttu-id="ac180-365">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ac180-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ac180-366">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ac180-367">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ac180-368">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ac180-369">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac180-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ac180-370">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="ac180-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="ac180-371">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="ac180-372">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="ac180-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="ac180-373">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="ac180-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="ac180-374">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="ac180-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="ac180-375">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="ac180-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="ac180-376">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="ac180-376">**Correct**:</span></span>

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

* <span data-ttu-id="ac180-377">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="ac180-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="ac180-378">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="ac180-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="ac180-379">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ac180-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="ac180-380">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="ac180-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ac180-381">Такие исключения случаются редко. Главным образом они связаны с особенностями самой платформы.</span><span class="sxs-lookup"><span data-stu-id="ac180-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ac180-382">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="ac180-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ac180-383">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="ac180-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="ac180-384">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-384">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="ac180-385">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="ac180-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="ac180-386">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="ac180-386">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="ac180-387">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="ac180-387">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac180-388">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ac180-388">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ac180-389">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ac180-389">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="ac180-390">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-390">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ac180-391">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="ac180-391">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="ac180-392">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ac180-392">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac180-393">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="ac180-393">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ac180-394">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ac180-394">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ac180-395">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac180-395">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ac180-396">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-396">Overview of dependency injection</span></span>

<span data-ttu-id="ac180-397">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="ac180-397">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="ac180-398">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="ac180-398">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="ac180-399">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-399">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ac180-400">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="ac180-400">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ac180-401">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="ac180-401">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ac180-402">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="ac180-402">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ac180-403">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="ac180-403">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="ac180-404">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="ac180-404">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="ac180-405">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="ac180-405">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ac180-406">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="ac180-406">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ac180-407">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="ac180-407">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ac180-408">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ac180-408">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ac180-409">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-409">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ac180-410">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-410">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ac180-411">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="ac180-411">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ac180-412">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-412">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ac180-413">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="ac180-413">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ac180-414">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="ac180-414">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ac180-415">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="ac180-415">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ac180-416">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-416">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ac180-417">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="ac180-417">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ac180-418">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="ac180-418">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ac180-419">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="ac180-419">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ac180-420">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="ac180-420">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ac180-421">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="ac180-421">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="ac180-422">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-422">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ac180-423">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-423">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac180-424">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="ac180-424">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="ac180-425">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="ac180-425">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ac180-426">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-426">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ac180-427">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="ac180-427">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="ac180-428">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="ac180-428">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ac180-429">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-429">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ac180-430">Например, `services.AddMvc()` добавляет службы, которые нужны для Razor Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="ac180-430">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="ac180-431">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="ac180-431">We recommended that apps follow this convention.</span></span> <span data-ttu-id="ac180-432">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-432">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ac180-433">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac180-433">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="ac180-434">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="ac180-434">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="ac180-435">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="ac180-435">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="ac180-436">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="ac180-436">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ac180-437">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="ac180-437">Services injected into Startup</span></span>

<span data-ttu-id="ac180-438">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="ac180-438">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ac180-439">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ac180-439">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ac180-440">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac180-440">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ac180-441">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="ac180-441">Framework-provided services</span></span>

<span data-ttu-id="ac180-442">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ac180-442">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ac180-443">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="ac180-443">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ac180-444">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="ac180-444">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="ac180-445">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-445">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="ac180-446">Тип службы</span><span class="sxs-lookup"><span data-stu-id="ac180-446">Service Type</span></span> | <span data-ttu-id="ac180-447">Время существования</span><span class="sxs-lookup"><span data-stu-id="ac180-447">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ac180-448">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="ac180-449">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="ac180-450">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-450">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ac180-451">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-451">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ac180-452">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-452">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ac180-453">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-453">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ac180-454">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ac180-455">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-455">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ac180-456">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-456">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ac180-457">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-457">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ac180-458">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-458">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ac180-459">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-459">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ac180-460">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-460">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ac180-461">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-461">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ac180-462">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="ac180-462">Register additional services with extension methods</span></span>

<span data-ttu-id="ac180-463">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="ac180-463">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="ac180-464">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="ac180-464">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="ac180-465">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="ac180-465">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ac180-466">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="ac180-466">Service lifetimes</span></span>

<span data-ttu-id="ac180-467">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-467">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="ac180-468">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="ac180-468">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="ac180-469">Временный</span><span class="sxs-lookup"><span data-stu-id="ac180-469">Transient</span></span>

<span data-ttu-id="ac180-470">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-470">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ac180-471">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-471">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="ac180-472">Область действия</span><span class="sxs-lookup"><span data-stu-id="ac180-472">Scoped</span></span>

<span data-ttu-id="ac180-473">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="ac180-473">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="ac180-474">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac180-474">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ac180-475">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="ac180-475">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ac180-476">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ac180-476">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ac180-477">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="ac180-477">Singleton</span></span>

<span data-ttu-id="ac180-478">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="ac180-478">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="ac180-479">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="ac180-479">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ac180-480">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-480">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="ac180-481">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="ac180-481">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="ac180-482">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="ac180-482">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ac180-483">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-483">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ac180-484">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="ac180-484">Service registration methods</span></span>

<span data-ttu-id="ac180-485">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="ac180-485">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="ac180-486">Метод</span><span class="sxs-lookup"><span data-stu-id="ac180-486">Method</span></span> | <span data-ttu-id="ac180-487">Автоматический</span><span class="sxs-lookup"><span data-stu-id="ac180-487">Automatic</span></span><br><span data-ttu-id="ac180-488">object</span><span class="sxs-lookup"><span data-stu-id="ac180-488">object</span></span><br><span data-ttu-id="ac180-489">удаление</span><span class="sxs-lookup"><span data-stu-id="ac180-489">disposal</span></span> | <span data-ttu-id="ac180-490">Несколько</span><span class="sxs-lookup"><span data-stu-id="ac180-490">Multiple</span></span><br><span data-ttu-id="ac180-491">реализации</span><span class="sxs-lookup"><span data-stu-id="ac180-491">implementations</span></span> | <span data-ttu-id="ac180-492">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="ac180-492">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ac180-493">Пример.</span><span class="sxs-lookup"><span data-stu-id="ac180-493">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ac180-494">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-494">Yes</span></span> | <span data-ttu-id="ac180-495">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-495">Yes</span></span> | <span data-ttu-id="ac180-496">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-496">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-497">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-497">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ac180-498">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-498">Yes</span></span> | <span data-ttu-id="ac180-499">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-499">Yes</span></span> | <span data-ttu-id="ac180-500">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-500">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ac180-501">Пример.</span><span class="sxs-lookup"><span data-stu-id="ac180-501">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ac180-502">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-502">Yes</span></span> | <span data-ttu-id="ac180-503">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-503">No</span></span> | <span data-ttu-id="ac180-504">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-504">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-505">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-505">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ac180-506">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-506">No</span></span> | <span data-ttu-id="ac180-507">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-507">Yes</span></span> | <span data-ttu-id="ac180-508">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-508">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ac180-509">Примеры</span><span class="sxs-lookup"><span data-stu-id="ac180-509">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ac180-510">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-510">No</span></span> | <span data-ttu-id="ac180-511">Нет</span><span class="sxs-lookup"><span data-stu-id="ac180-511">No</span></span> | <span data-ttu-id="ac180-512">Да</span><span class="sxs-lookup"><span data-stu-id="ac180-512">Yes</span></span> |

<span data-ttu-id="ac180-513">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="ac180-513">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ac180-514">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ac180-514">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ac180-515">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="ac180-515">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ac180-516">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac180-516">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="ac180-517">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="ac180-517">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ac180-518">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="ac180-518">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ac180-519">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="ac180-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ac180-520">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="ac180-520">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ac180-521">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="ac180-521">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ac180-522">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="ac180-522">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="ac180-523">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="ac180-523">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ac180-524">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="ac180-524">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="ac180-525">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="ac180-525">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ac180-526">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="ac180-526">Constructor injection behavior</span></span>

<span data-ttu-id="ac180-527">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="ac180-527">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ac180-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="ac180-529">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="ac180-529">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ac180-530">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ac180-530">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ac180-531">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="ac180-531">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ac180-532">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="ac180-532">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ac180-533">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-533">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ac180-534">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ac180-534">Entity Framework contexts</span></span>

<span data-ttu-id="ac180-535">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="ac180-535">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ac180-536">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="ac180-536">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ac180-537">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-537">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ac180-538">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="ac180-538">Lifetime and registration options</span></span>

<span data-ttu-id="ac180-539">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-539">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="ac180-540">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-540">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ac180-541">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-541">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ac180-542">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="ac180-542">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ac180-543">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-543">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ac180-544">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-544">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="ac180-545">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-545">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="ac180-546">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="ac180-546">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="ac180-547">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-547">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="ac180-548">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="ac180-548">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="ac180-549">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac180-549">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="ac180-550">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-550">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ac180-551">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="ac180-551">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ac180-552">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ac180-552">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="ac180-553">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="ac180-553">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ac180-554">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-554">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="ac180-555">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-555">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="ac180-556">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="ac180-556">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ac180-557">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="ac180-557">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="ac180-558">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="ac180-558">**First request:**</span></span>

<span data-ttu-id="ac180-559">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="ac180-559">Controller operations:</span></span>

<span data-ttu-id="ac180-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ac180-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ac180-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac180-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac180-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-563">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-563">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-564">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac180-564">`OperationService` operations:</span></span>

<span data-ttu-id="ac180-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ac180-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="ac180-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac180-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac180-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-568">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-568">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-569">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="ac180-569">**Second request:**</span></span>

<span data-ttu-id="ac180-570">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="ac180-570">Controller operations:</span></span>

<span data-ttu-id="ac180-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ac180-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ac180-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac180-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac180-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-574">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-574">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-575">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac180-575">`OperationService` operations:</span></span>

<span data-ttu-id="ac180-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ac180-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="ac180-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac180-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac180-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac180-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac180-579">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac180-579">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac180-580">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-580">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="ac180-581">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="ac180-581">*Transient* objects are always different.</span></span> <span data-ttu-id="ac180-582">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac180-582">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ac180-583">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="ac180-583">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="ac180-584">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="ac180-584">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="ac180-585">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac180-585">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ac180-586">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="ac180-586">Call services from main</span></span>

<span data-ttu-id="ac180-587">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="ac180-587">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ac180-588">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="ac180-588">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="ac180-589">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac180-589">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ac180-590">Проверка области</span><span class="sxs-lookup"><span data-stu-id="ac180-590">Scope validation</span></span>

<span data-ttu-id="ac180-591">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="ac180-591">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ac180-592">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="ac180-592">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ac180-593">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="ac180-593">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ac180-594">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="ac180-594">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="ac180-595">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="ac180-595">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ac180-596">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="ac180-596">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ac180-597">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="ac180-597">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ac180-598">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="ac180-598">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ac180-599">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="ac180-599">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="ac180-600">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="ac180-600">Request Services</span></span>

<span data-ttu-id="ac180-601">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="ac180-601">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="ac180-602">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-602">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="ac180-603">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-603">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ac180-604">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="ac180-604">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="ac180-605">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="ac180-605">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="ac180-606">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="ac180-606">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="ac180-607">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="ac180-607">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ac180-608">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-608">Design services for dependency injection</span></span>

<span data-ttu-id="ac180-609">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="ac180-609">Best practices are to:</span></span>

* <span data-ttu-id="ac180-610">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-610">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="ac180-611">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-611">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ac180-612">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="ac180-612">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="ac180-613">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-613">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ac180-614">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="ac180-614">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ac180-615">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="ac180-615">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ac180-616">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ac180-616">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ac180-617">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="ac180-617">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="ac180-618">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="ac180-618">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="ac180-619">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="ac180-619">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ac180-620">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="ac180-620">Disposal of services</span></span>

<span data-ttu-id="ac180-621">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="ac180-621">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ac180-622">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="ac180-622">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="ac180-623">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="ac180-623">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="ac180-624">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ac180-624">In the following example:</span></span>

* <span data-ttu-id="ac180-625">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-625">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ac180-626">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-626">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="ac180-627">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="ac180-627">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ac180-628">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ac180-628">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="ac180-629">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="ac180-629">Default service container replacement</span></span>

<span data-ttu-id="ac180-630">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="ac180-630">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ac180-631">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="ac180-631">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="ac180-632">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="ac180-632">Property injection</span></span>
* <span data-ttu-id="ac180-633">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="ac180-633">Injection based on name</span></span>
* <span data-ttu-id="ac180-634">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="ac180-634">Child containers</span></span>
* <span data-ttu-id="ac180-635">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="ac180-635">Custom lifetime management</span></span>
* <span data-ttu-id="ac180-636">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="ac180-636">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ac180-637">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="ac180-637">Convention-based registration</span></span>

<span data-ttu-id="ac180-638">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="ac180-638">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="ac180-639">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="ac180-639">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="ac180-640">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-640">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="ac180-641">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="ac180-641">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="ac180-642">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-642">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="ac180-643">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="ac180-643">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="ac180-644">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="ac180-644">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="ac180-645">Unity</span><span class="sxs-lookup"><span data-stu-id="ac180-645">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="ac180-646">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="ac180-646">Thread safety</span></span>

<span data-ttu-id="ac180-647">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-647">Create thread-safe singleton services.</span></span> <span data-ttu-id="ac180-648">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="ac180-648">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="ac180-649">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="ac180-649">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ac180-650">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="ac180-650">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ac180-651">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="ac180-651">Recommendations</span></span>

* <span data-ttu-id="ac180-652">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ac180-652">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ac180-653">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="ac180-653">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ac180-654">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-654">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ac180-655">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="ac180-655">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ac180-656">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac180-656">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ac180-657">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="ac180-657">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="ac180-658">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="ac180-658">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="ac180-659">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="ac180-659">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="ac180-660">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="ac180-660">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="ac180-661">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="ac180-661">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="ac180-662">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="ac180-662">**Incorrect:**</span></span>

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

    <span data-ttu-id="ac180-663">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="ac180-663">**Correct**:</span></span>

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

  * <span data-ttu-id="ac180-664">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="ac180-664">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="ac180-665">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ac180-665">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="ac180-666">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="ac180-666">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ac180-667">Такие исключения случаются редко. Главным образом они связаны с особенностями самой платформы.</span><span class="sxs-lookup"><span data-stu-id="ac180-667">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ac180-668">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="ac180-668">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ac180-669">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="ac180-669">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac180-670">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ac180-670">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ac180-671">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ac180-671">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="ac180-672">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="ac180-672">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ac180-673">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="ac180-673">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="ac180-674">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ac180-674">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
