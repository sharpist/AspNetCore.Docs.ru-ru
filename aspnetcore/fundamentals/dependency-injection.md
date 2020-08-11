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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: fcfa9e93228cdf71b33e67aeab38fdd9a3295b75
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819221"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="a1ba0-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1ba0-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="a1ba0-104">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1ba0-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="a1ba0-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="a1ba0-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1ba0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="a1ba0-108">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-108">Overview of dependency injection</span></span>

<span data-ttu-id="a1ba0-109">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="a1ba0-110">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="a1ba0-111">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="a1ba0-112">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="a1ba0-113">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="a1ba0-114">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="a1ba0-115">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="a1ba0-116">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="a1ba0-117">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="a1ba0-118">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="a1ba0-119">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="a1ba0-120">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="a1ba0-121">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="a1ba0-122">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="a1ba0-123">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a1ba0-124">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="a1ba0-125">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="a1ba0-126">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="a1ba0-127">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="a1ba0-128">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="a1ba0-129">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="a1ba0-130">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="a1ba0-131">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="a1ba0-132">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="a1ba0-133">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="a1ba0-134">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="a1ba0-135">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a1ba0-136">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="a1ba0-137">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="a1ba0-138">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="a1ba0-139">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="a1ba0-140">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="a1ba0-141">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="a1ba0-142">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="a1ba0-143">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="a1ba0-144">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="a1ba0-145">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="a1ba0-146">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="a1ba0-147">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="a1ba0-148">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="a1ba0-149">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="a1ba0-149">Services injected into Startup</span></span>

<span data-ttu-id="a1ba0-150">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="a1ba0-151">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="a1ba0-152">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="a1ba0-153">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-153">Framework-provided services</span></span>

<span data-ttu-id="a1ba0-154">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="a1ba0-155">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="a1ba0-156">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="a1ba0-157">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="a1ba0-158">Тип службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-158">Service Type</span></span> | <span data-ttu-id="a1ba0-159">Время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-160">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="a1ba0-161">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="a1ba0-162">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="a1ba0-163">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="a1ba0-164">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="a1ba0-165">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-166">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-167">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-168">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="a1ba0-169">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-170">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-171">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="a1ba0-172">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="a1ba0-173">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="a1ba0-174">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="a1ba0-174">Register additional services with extension methods</span></span>

<span data-ttu-id="a1ba0-175">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="a1ba0-176">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="a1ba0-177">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="a1ba0-178">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-178">Service lifetimes</span></span>

<span data-ttu-id="a1ba0-179">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="a1ba0-180">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="a1ba0-181">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-181">Transient</span></span>

<span data-ttu-id="a1ba0-182">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="a1ba0-183">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="a1ba0-184">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="a1ba0-185">Область действия</span><span class="sxs-lookup"><span data-stu-id="a1ba0-185">Scoped</span></span>

<span data-ttu-id="a1ba0-186">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="a1ba0-187">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="a1ba0-188">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="a1ba0-189">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="a1ba0-190">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="a1ba0-191">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-191">Singleton</span></span>

<span data-ttu-id="a1ba0-192">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="a1ba0-193">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="a1ba0-194">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="a1ba0-195">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="a1ba0-196">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="a1ba0-197">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="a1ba0-198">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="a1ba0-199">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-199">Service registration methods</span></span>

<span data-ttu-id="a1ba0-200">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="a1ba0-201">Метод</span><span class="sxs-lookup"><span data-stu-id="a1ba0-201">Method</span></span> | <span data-ttu-id="a1ba0-202">Автоматически</span><span class="sxs-lookup"><span data-stu-id="a1ba0-202">Automatic</span></span><br><span data-ttu-id="a1ba0-203">object</span><span class="sxs-lookup"><span data-stu-id="a1ba0-203">object</span></span><br><span data-ttu-id="a1ba0-204">удаление</span><span class="sxs-lookup"><span data-stu-id="a1ba0-204">disposal</span></span> | <span data-ttu-id="a1ba0-205">Несколько</span><span class="sxs-lookup"><span data-stu-id="a1ba0-205">Multiple</span></span><br><span data-ttu-id="a1ba0-206">реализации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-206">implementations</span></span> | <span data-ttu-id="a1ba0-207">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="a1ba0-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="a1ba0-208">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="a1ba0-209">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-209">Yes</span></span> | <span data-ttu-id="a1ba0-210">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-210">Yes</span></span> | <span data-ttu-id="a1ba0-211">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-212">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="a1ba0-213">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-213">Yes</span></span> | <span data-ttu-id="a1ba0-214">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-214">Yes</span></span> | <span data-ttu-id="a1ba0-215">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="a1ba0-216">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="a1ba0-217">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-217">Yes</span></span> | <span data-ttu-id="a1ba0-218">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-218">No</span></span> | <span data-ttu-id="a1ba0-219">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-220">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="a1ba0-221">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-221">No</span></span> | <span data-ttu-id="a1ba0-222">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-222">Yes</span></span> | <span data-ttu-id="a1ba0-223">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-224">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="a1ba0-225">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-225">No</span></span> | <span data-ttu-id="a1ba0-226">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-226">No</span></span> | <span data-ttu-id="a1ba0-227">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-227">Yes</span></span> |

<span data-ttu-id="a1ba0-228">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="a1ba0-229">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="a1ba0-230">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="a1ba0-231">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="a1ba0-232">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="a1ba0-233">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="a1ba0-234">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="a1ba0-235">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="a1ba0-236">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="a1ba0-237">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="a1ba0-238">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="a1ba0-239">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="a1ba0-240">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="a1ba0-241">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="a1ba0-241">Constructor injection behavior</span></span>

<span data-ttu-id="a1ba0-242">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="a1ba0-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="a1ba0-244">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="a1ba0-245">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="a1ba0-246">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="a1ba0-247">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="a1ba0-248">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="a1ba0-249">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="a1ba0-249">Entity Framework contexts</span></span>

<span data-ttu-id="a1ba0-250">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="a1ba0-251">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="a1ba0-252">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="a1ba0-253">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-253">Lifetime and registration options</span></span>

<span data-ttu-id="a1ba0-254">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="a1ba0-255">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="a1ba0-256">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="a1ba0-257">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="a1ba0-258">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="a1ba0-259">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="a1ba0-260">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="a1ba0-261">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="a1ba0-262">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="a1ba0-263">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="a1ba0-264">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="a1ba0-265">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="a1ba0-266">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="a1ba0-267">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="a1ba0-268">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="a1ba0-269">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="a1ba0-270">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="a1ba0-271">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="a1ba0-272">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="a1ba0-273">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-273">**First request:**</span></span>

<span data-ttu-id="a1ba0-274">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-274">Controller operations:</span></span>

<span data-ttu-id="a1ba0-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="a1ba0-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="a1ba0-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="a1ba0-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="a1ba0-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-278">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-279">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-279">`OperationService` operations:</span></span>

<span data-ttu-id="a1ba0-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="a1ba0-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="a1ba0-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="a1ba0-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="a1ba0-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-283">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-284">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-284">**Second request:**</span></span>

<span data-ttu-id="a1ba0-285">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-285">Controller operations:</span></span>

<span data-ttu-id="a1ba0-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="a1ba0-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="a1ba0-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="a1ba0-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="a1ba0-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-289">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-290">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-290">`OperationService` operations:</span></span>

<span data-ttu-id="a1ba0-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="a1ba0-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="a1ba0-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="a1ba0-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="a1ba0-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-294">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-295">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="a1ba0-296">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-296">*Transient* objects are always different.</span></span> <span data-ttu-id="a1ba0-297">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="a1ba0-298">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="a1ba0-299">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="a1ba0-300">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="a1ba0-301">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="a1ba0-301">Call services from main</span></span>

<span data-ttu-id="a1ba0-302">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="a1ba0-303">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="a1ba0-304">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="a1ba0-305">Проверка области</span><span class="sxs-lookup"><span data-stu-id="a1ba0-305">Scope validation</span></span>

<span data-ttu-id="a1ba0-306">Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="a1ba0-307">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="a1ba0-308">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="a1ba0-309">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="a1ba0-310">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="a1ba0-311">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="a1ba0-312">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="a1ba0-313">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="a1ba0-314">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="a1ba0-315">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="a1ba0-315">Request Services</span></span>

<span data-ttu-id="a1ba0-316">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="a1ba0-317">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="a1ba0-318">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="a1ba0-319">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="a1ba0-320">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="a1ba0-321">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-321">This yields classes that are easier to test.</span></span>

<span data-ttu-id="a1ba0-322">ASP.NET Core создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-322">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="a1ba0-323">Все службы с заданной областью действительны до тех пор, пока запрос активен.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-323">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="a1ba0-324">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="a1ba0-325">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-325">Design services for dependency injection</span></span>

<span data-ttu-id="a1ba0-326">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-326">Best practices are to:</span></span>

* <span data-ttu-id="a1ba0-327">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-327">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="a1ba0-328">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="a1ba0-329">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-329">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="a1ba0-330">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="a1ba0-331">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-331">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="a1ba0-332">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="a1ba0-333">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="a1ba0-334">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="a1ba0-335">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="a1ba0-336">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="a1ba0-337">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-337">Disposal of services</span></span>

<span data-ttu-id="a1ba0-338">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="a1ba0-339">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="a1ba0-340">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="a1ba0-341">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-341">In the following example:</span></span>

* <span data-ttu-id="a1ba0-342">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-342">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="a1ba0-343">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-343">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="a1ba0-344">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-344">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="a1ba0-345">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="a1ba0-346">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="a1ba0-346">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="a1ba0-347">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-347">Transient, limited lifetime</span></span>

<span data-ttu-id="a1ba0-348">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-348">**Scenario**</span></span>

<span data-ttu-id="a1ba0-349">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="a1ba0-350">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-350">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="a1ba0-351">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-351">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="a1ba0-352">**Решение**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-352">**Solution**</span></span>

<span data-ttu-id="a1ba0-353">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-353">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="a1ba0-354">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="a1ba0-355">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-355">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="a1ba0-356">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="a1ba0-357">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="a1ba0-358">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-358">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="a1ba0-359">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-359">**Scenario**</span></span>

<span data-ttu-id="a1ba0-360">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="a1ba0-361">**Решение**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-361">**Solution**</span></span>

<span data-ttu-id="a1ba0-362">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="a1ba0-363">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="a1ba0-364">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="a1ba0-365">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-365">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="a1ba0-366">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-366">General Guidelines</span></span>

* <span data-ttu-id="a1ba0-367">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="a1ba0-368">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-368">Use the factory pattern instead.</span></span>
* <span data-ttu-id="a1ba0-369">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="a1ba0-370">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="a1ba0-371">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="a1ba0-372">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="a1ba0-373">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-373">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="a1ba0-374">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="a1ba0-375">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-375">Default service container replacement</span></span>

<span data-ttu-id="a1ba0-376">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="a1ba0-377">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="a1ba0-378">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="a1ba0-378">Property injection</span></span>
* <span data-ttu-id="a1ba0-379">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="a1ba0-379">Injection based on name</span></span>
* <span data-ttu-id="a1ba0-380">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="a1ba0-380">Child containers</span></span>
* <span data-ttu-id="a1ba0-381">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-381">Custom lifetime management</span></span>
* <span data-ttu-id="a1ba0-382">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="a1ba0-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="a1ba0-383">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="a1ba0-383">Convention-based registration</span></span>

<span data-ttu-id="a1ba0-384">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-384">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="a1ba0-385">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-385">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="a1ba0-386">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-386">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="a1ba0-387">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-387">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="a1ba0-388">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-388">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="a1ba0-389">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-389">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="a1ba0-390">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-390">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="a1ba0-391">Unity</span><span class="sxs-lookup"><span data-stu-id="a1ba0-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="a1ba0-392">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="a1ba0-392">Thread safety</span></span>

<span data-ttu-id="a1ba0-393">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="a1ba0-394">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="a1ba0-395">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="a1ba0-396">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="a1ba0-397">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-397">Recommendations</span></span>

* <span data-ttu-id="a1ba0-398">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="a1ba0-399">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="a1ba0-400">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-400">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="a1ba0-401">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="a1ba0-402">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a1ba0-403">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="a1ba0-404">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-404">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="a1ba0-405">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="a1ba0-406">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-406">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="a1ba0-407">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="a1ba0-408">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-408">**Incorrect:**</span></span>

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

  <span data-ttu-id="a1ba0-409">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-409">**Correct**:</span></span>

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

* <span data-ttu-id="a1ba0-410">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="a1ba0-411">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="a1ba0-412">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="a1ba0-413">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="a1ba0-414">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="a1ba0-415">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-415">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="a1ba0-416">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="a1ba0-417">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-417">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="a1ba0-418">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="a1ba0-419">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="a1ba0-420">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1ba0-421">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-421">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="a1ba0-422">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1ba0-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="a1ba0-423">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="a1ba0-424">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="a1ba0-425">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="a1ba0-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a1ba0-427">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="a1ba0-428">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="a1ba0-429">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1ba0-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="a1ba0-430">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-430">Overview of dependency injection</span></span>

<span data-ttu-id="a1ba0-431">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-431">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="a1ba0-432">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="a1ba0-433">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="a1ba0-434">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="a1ba0-435">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="a1ba0-436">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="a1ba0-437">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="a1ba0-438">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="a1ba0-439">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="a1ba0-440">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="a1ba0-441">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="a1ba0-442">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-442">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="a1ba0-443">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-443">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="a1ba0-444">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-444">Registration of the dependency in a service container.</span></span> <span data-ttu-id="a1ba0-445">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a1ba0-446">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="a1ba0-447">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="a1ba0-448">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="a1ba0-449">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="a1ba0-450">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="a1ba0-451">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="a1ba0-452">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="a1ba0-453">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-453">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="a1ba0-454">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="a1ba0-455">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="a1ba0-456">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="a1ba0-457">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a1ba0-458">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="a1ba0-459">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="a1ba0-460">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="a1ba0-461">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="a1ba0-462">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="a1ba0-463">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="a1ba0-464">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="a1ba0-465">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-465">We recommended that apps follow this convention.</span></span> <span data-ttu-id="a1ba0-466">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="a1ba0-467">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="a1ba0-468">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="a1ba0-469">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-469">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="a1ba0-470">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="a1ba0-471">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="a1ba0-471">Services injected into Startup</span></span>

<span data-ttu-id="a1ba0-472">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="a1ba0-473">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-473">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="a1ba0-474">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-474">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="a1ba0-475">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-475">Framework-provided services</span></span>

<span data-ttu-id="a1ba0-476">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="a1ba0-477">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="a1ba0-478">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="a1ba0-479">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-479">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="a1ba0-480">Тип службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-480">Service Type</span></span> | <span data-ttu-id="a1ba0-481">Время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-481">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-482">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="a1ba0-483">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="a1ba0-484">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-484">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="a1ba0-485">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="a1ba0-486">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-486">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="a1ba0-487">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-487">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-488">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-488">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-489">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="a1ba0-490">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-490">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="a1ba0-491">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-491">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-492">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-492">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="a1ba0-493">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-493">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="a1ba0-494">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-494">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="a1ba0-495">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-495">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="a1ba0-496">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="a1ba0-496">Register additional services with extension methods</span></span>

<span data-ttu-id="a1ba0-497">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="a1ba0-498">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="a1ba0-499">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="a1ba0-500">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-500">Service lifetimes</span></span>

<span data-ttu-id="a1ba0-501">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-501">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="a1ba0-502">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="a1ba0-503">Временный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-503">Transient</span></span>

<span data-ttu-id="a1ba0-504">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="a1ba0-505">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-505">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="a1ba0-506">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-506">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="a1ba0-507">Область действия</span><span class="sxs-lookup"><span data-stu-id="a1ba0-507">Scoped</span></span>

<span data-ttu-id="a1ba0-508">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-508">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="a1ba0-509">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-509">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="a1ba0-510">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-510">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="a1ba0-511">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-511">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="a1ba0-512">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-512">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="a1ba0-513">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="a1ba0-513">Singleton</span></span>

<span data-ttu-id="a1ba0-514">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-514">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="a1ba0-515">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-515">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="a1ba0-516">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-516">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="a1ba0-517">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-517">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="a1ba0-518">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-518">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="a1ba0-519">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-519">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="a1ba0-520">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-520">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="a1ba0-521">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-521">Service registration methods</span></span>

<span data-ttu-id="a1ba0-522">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-522">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="a1ba0-523">Метод</span><span class="sxs-lookup"><span data-stu-id="a1ba0-523">Method</span></span> | <span data-ttu-id="a1ba0-524">Автоматически</span><span class="sxs-lookup"><span data-stu-id="a1ba0-524">Automatic</span></span><br><span data-ttu-id="a1ba0-525">object</span><span class="sxs-lookup"><span data-stu-id="a1ba0-525">object</span></span><br><span data-ttu-id="a1ba0-526">удаление</span><span class="sxs-lookup"><span data-stu-id="a1ba0-526">disposal</span></span> | <span data-ttu-id="a1ba0-527">Несколько</span><span class="sxs-lookup"><span data-stu-id="a1ba0-527">Multiple</span></span><br><span data-ttu-id="a1ba0-528">реализации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-528">implementations</span></span> | <span data-ttu-id="a1ba0-529">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="a1ba0-529">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="a1ba0-530">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-530">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="a1ba0-531">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-531">Yes</span></span> | <span data-ttu-id="a1ba0-532">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-532">Yes</span></span> | <span data-ttu-id="a1ba0-533">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-533">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-534">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="a1ba0-535">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-535">Yes</span></span> | <span data-ttu-id="a1ba0-536">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-536">Yes</span></span> | <span data-ttu-id="a1ba0-537">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-537">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="a1ba0-538">Пример.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-538">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="a1ba0-539">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-539">Yes</span></span> | <span data-ttu-id="a1ba0-540">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-540">No</span></span> | <span data-ttu-id="a1ba0-541">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-541">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-542">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-542">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="a1ba0-543">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-543">No</span></span> | <span data-ttu-id="a1ba0-544">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-544">Yes</span></span> | <span data-ttu-id="a1ba0-545">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-545">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="a1ba0-546">Примеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-546">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="a1ba0-547">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-547">No</span></span> | <span data-ttu-id="a1ba0-548">Нет</span><span class="sxs-lookup"><span data-stu-id="a1ba0-548">No</span></span> | <span data-ttu-id="a1ba0-549">Да</span><span class="sxs-lookup"><span data-stu-id="a1ba0-549">Yes</span></span> |

<span data-ttu-id="a1ba0-550">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-550">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="a1ba0-551">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-551">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="a1ba0-552">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-552">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="a1ba0-553">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-553">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="a1ba0-554">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-554">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="a1ba0-555">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-555">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="a1ba0-556">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-556">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="a1ba0-557">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-557">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="a1ba0-558">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-558">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="a1ba0-559">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-559">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="a1ba0-560">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-560">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="a1ba0-561">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-561">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="a1ba0-562">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-562">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="a1ba0-563">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="a1ba0-563">Constructor injection behavior</span></span>

<span data-ttu-id="a1ba0-564">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-564">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="a1ba0-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="a1ba0-566">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-566">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="a1ba0-567">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-567">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="a1ba0-568">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-568">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="a1ba0-569">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-569">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="a1ba0-570">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-570">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="a1ba0-571">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="a1ba0-571">Entity Framework contexts</span></span>

<span data-ttu-id="a1ba0-572">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-572">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="a1ba0-573">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-573">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="a1ba0-574">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-574">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="a1ba0-575">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-575">Lifetime and registration options</span></span>

<span data-ttu-id="a1ba0-576">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-576">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="a1ba0-577">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-577">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="a1ba0-578">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-578">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="a1ba0-579">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-579">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="a1ba0-580">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-580">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="a1ba0-581">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-581">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="a1ba0-582">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-582">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="a1ba0-583">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-583">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="a1ba0-584">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-584">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="a1ba0-585">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-585">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="a1ba0-586">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-586">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="a1ba0-587">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-587">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="a1ba0-588">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-588">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="a1ba0-589">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-589">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="a1ba0-590">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-590">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="a1ba0-591">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-591">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="a1ba0-592">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-592">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="a1ba0-593">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-593">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="a1ba0-594">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-594">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="a1ba0-595">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-595">**First request:**</span></span>

<span data-ttu-id="a1ba0-596">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-596">Controller operations:</span></span>

<span data-ttu-id="a1ba0-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="a1ba0-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="a1ba0-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="a1ba0-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="a1ba0-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-600">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-600">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-601">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-601">`OperationService` operations:</span></span>

<span data-ttu-id="a1ba0-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="a1ba0-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="a1ba0-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="a1ba0-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="a1ba0-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-605">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-605">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-606">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-606">**Second request:**</span></span>

<span data-ttu-id="a1ba0-607">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-607">Controller operations:</span></span>

<span data-ttu-id="a1ba0-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="a1ba0-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="a1ba0-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="a1ba0-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="a1ba0-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-611">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-611">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-612">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-612">`OperationService` operations:</span></span>

<span data-ttu-id="a1ba0-613">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="a1ba0-613">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="a1ba0-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="a1ba0-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="a1ba0-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="a1ba0-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="a1ba0-616">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="a1ba0-616">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="a1ba0-617">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-617">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="a1ba0-618">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-618">*Transient* objects are always different.</span></span> <span data-ttu-id="a1ba0-619">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-619">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="a1ba0-620">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-620">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="a1ba0-621">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-621">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="a1ba0-622">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-622">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="a1ba0-623">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="a1ba0-623">Call services from main</span></span>

<span data-ttu-id="a1ba0-624">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-624">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="a1ba0-625">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-625">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="a1ba0-626">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-626">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="a1ba0-627">Проверка области</span><span class="sxs-lookup"><span data-stu-id="a1ba0-627">Scope validation</span></span>

<span data-ttu-id="a1ba0-628">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-628">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="a1ba0-629">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-629">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="a1ba0-630">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-630">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="a1ba0-631">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-631">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="a1ba0-632">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-632">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="a1ba0-633">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-633">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="a1ba0-634">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-634">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="a1ba0-635">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-635">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="a1ba0-636">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-636">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="a1ba0-637">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="a1ba0-637">Request Services</span></span>

<span data-ttu-id="a1ba0-638">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-638">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="a1ba0-639">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-639">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="a1ba0-640">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-640">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="a1ba0-641">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-641">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="a1ba0-642">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-642">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="a1ba0-643">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-643">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="a1ba0-644">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-644">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="a1ba0-645">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-645">Design services for dependency injection</span></span>

<span data-ttu-id="a1ba0-646">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-646">Best practices are to:</span></span>

* <span data-ttu-id="a1ba0-647">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-647">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="a1ba0-648">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-648">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="a1ba0-649">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-649">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="a1ba0-650">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-650">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="a1ba0-651">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-651">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="a1ba0-652">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-652">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="a1ba0-653">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-653">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="a1ba0-654">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-654">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="a1ba0-655">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-655">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="a1ba0-656">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-656">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="a1ba0-657">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-657">Disposal of services</span></span>

<span data-ttu-id="a1ba0-658">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-658">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="a1ba0-659">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-659">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="a1ba0-660">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-660">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="a1ba0-661">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-661">In the following example:</span></span>

* <span data-ttu-id="a1ba0-662">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-662">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="a1ba0-663">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-663">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="a1ba0-664">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-664">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="a1ba0-665">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-665">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="a1ba0-666">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="a1ba0-666">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="a1ba0-667">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-667">Transient, limited lifetime</span></span>

<span data-ttu-id="a1ba0-668">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-668">**Scenario**</span></span>

<span data-ttu-id="a1ba0-669">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-669">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="a1ba0-670">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-670">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="a1ba0-671">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-671">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="a1ba0-672">**Решение**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-672">**Solution**</span></span>

<span data-ttu-id="a1ba0-673">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-673">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="a1ba0-674">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-674">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="a1ba0-675">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-675">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="a1ba0-676">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-676">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="a1ba0-677">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-677">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="a1ba0-678">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-678">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="a1ba0-679">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-679">**Scenario**</span></span>

<span data-ttu-id="a1ba0-680">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-680">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="a1ba0-681">**Решение**</span><span class="sxs-lookup"><span data-stu-id="a1ba0-681">**Solution**</span></span>

<span data-ttu-id="a1ba0-682">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-682">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="a1ba0-683">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="a1ba0-684">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-684">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="a1ba0-685">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-685">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="a1ba0-686">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-686">General Guidelines</span></span>

* <span data-ttu-id="a1ba0-687">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-687">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="a1ba0-688">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-688">Use the factory pattern instead.</span></span>
* <span data-ttu-id="a1ba0-689">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-689">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="a1ba0-690">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-690">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="a1ba0-691">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-691">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="a1ba0-692">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-692">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="a1ba0-693">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-693">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="a1ba0-694">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-694">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="a1ba0-695">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="a1ba0-695">Default service container replacement</span></span>

<span data-ttu-id="a1ba0-696">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-696">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="a1ba0-697">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-697">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="a1ba0-698">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="a1ba0-698">Property injection</span></span>
* <span data-ttu-id="a1ba0-699">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="a1ba0-699">Injection based on name</span></span>
* <span data-ttu-id="a1ba0-700">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="a1ba0-700">Child containers</span></span>
* <span data-ttu-id="a1ba0-701">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="a1ba0-701">Custom lifetime management</span></span>
* <span data-ttu-id="a1ba0-702">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="a1ba0-702">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="a1ba0-703">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="a1ba0-703">Convention-based registration</span></span>

<span data-ttu-id="a1ba0-704">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-704">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="a1ba0-705">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-705">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="a1ba0-706">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-706">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="a1ba0-707">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-707">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="a1ba0-708">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-708">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="a1ba0-709">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-709">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="a1ba0-710">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="a1ba0-710">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="a1ba0-711">Unity</span><span class="sxs-lookup"><span data-stu-id="a1ba0-711">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="a1ba0-712">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="a1ba0-712">Thread safety</span></span>

<span data-ttu-id="a1ba0-713">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-713">Create thread-safe singleton services.</span></span> <span data-ttu-id="a1ba0-714">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-714">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="a1ba0-715">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-715">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="a1ba0-716">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-716">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="a1ba0-717">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="a1ba0-717">Recommendations</span></span>

* <span data-ttu-id="a1ba0-718">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-718">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="a1ba0-719">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-719">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="a1ba0-720">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-720">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="a1ba0-721">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-721">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="a1ba0-722">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-722">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a1ba0-723">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-723">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="a1ba0-724">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-724">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="a1ba0-725">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-725">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="a1ba0-726">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-726">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="a1ba0-727">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-727">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="a1ba0-728">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-728">**Incorrect:**</span></span>

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

    <span data-ttu-id="a1ba0-729">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="a1ba0-729">**Correct**:</span></span>

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

  * <span data-ttu-id="a1ba0-730">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-730">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="a1ba0-731">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="a1ba0-731">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="a1ba0-732">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-732">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="a1ba0-733">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-733">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="a1ba0-734">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-734">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="a1ba0-735">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="a1ba0-735">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1ba0-736">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a1ba0-736">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="a1ba0-737">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1ba0-737">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="a1ba0-738">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-738">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="a1ba0-739">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="a1ba0-739">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="a1ba0-740">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-740">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="a1ba0-741">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="a1ba0-741">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
