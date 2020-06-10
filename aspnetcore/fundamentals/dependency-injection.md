---
title: 'title: Внедрение зависимостей в ASP.NET Core author: rick-anderson description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 14.05.2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271908"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="5fc71-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5fc71-103">'Blazor'</span></span>

<span data-ttu-id="5fc71-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5fc71-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5fc71-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5fc71-105">'Let's Encrypt'</span></span>

<span data-ttu-id="5fc71-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5fc71-106">'Razor'</span></span>

<span data-ttu-id="5fc71-107">'SignalR' uid: fundamentals/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="5fc71-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="5fc71-108">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fc71-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="5fc71-109">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="5fc71-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span> <span data-ttu-id="5fc71-110">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="5fc71-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="5fc71-111">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="5fc71-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fc71-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="5fc71-113">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-113">Overview of dependency injection</span></span> <span data-ttu-id="5fc71-114">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="5fc71-114">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="5fc71-115">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="5fc71-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="5fc71-116">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="5fc71-117">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="5fc71-118">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="5fc71-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="5fc71-119">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="5fc71-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="5fc71-120">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="5fc71-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="5fc71-121">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="5fc71-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="5fc71-122">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="5fc71-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="5fc71-123">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="5fc71-124">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="5fc71-125">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5fc71-125">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="5fc71-126">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-126">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="5fc71-127">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-127">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="5fc71-128">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="5fc71-129">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5fc71-130">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="5fc71-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="5fc71-131">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="5fc71-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="5fc71-132">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="5fc71-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="5fc71-133">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="5fc71-134">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="5fc71-135">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="5fc71-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="5fc71-136">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-136">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="5fc71-137">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="5fc71-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="5fc71-138">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="5fc71-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="5fc71-139">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="5fc71-140">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="5fc71-141">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="5fc71-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="5fc71-142">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="5fc71-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="5fc71-143">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="5fc71-144">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="5fc71-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="5fc71-145">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="5fc71-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="5fc71-146">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="5fc71-147">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="5fc71-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="5fc71-148">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="5fc71-148">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="5fc71-149">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="5fc71-150">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5fc71-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="5fc71-151">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="5fc71-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="5fc71-152">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="5fc71-152">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5fc71-153">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="5fc71-154">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="5fc71-154">Services injected into Startup</span></span> <span data-ttu-id="5fc71-155">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="5fc71-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="5fc71-156">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-156">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="5fc71-157">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-157">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="5fc71-158">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-158">Framework-provided services</span></span> | <span data-ttu-id="5fc71-159">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5fc71-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-160">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="5fc71-161">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="5fc71-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="5fc71-162">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-162">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="5fc71-163">Тип службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-163">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="5fc71-164">Время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-164">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="5fc71-165">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-165">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-166">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-166">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="5fc71-167">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-168">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="5fc71-169">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-169">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="5fc71-170">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-170">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="5fc71-171">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-171">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="5fc71-172">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="5fc71-173">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="5fc71-174">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-174">Singleton</span></span>

<span data-ttu-id="5fc71-175">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-175">Transient</span></span> <span data-ttu-id="5fc71-176">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-176">Singleton</span></span>

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

<span data-ttu-id="5fc71-177">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-177">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="5fc71-178">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-178">Singleton</span></span>

<span data-ttu-id="5fc71-179">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="5fc71-179">Register additional services with extension methods</span></span> <span data-ttu-id="5fc71-180">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="5fc71-181">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="5fc71-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="5fc71-182">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="5fc71-183">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-183">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="5fc71-184">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-184">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="5fc71-185">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="5fc71-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="5fc71-186">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-186">Transient</span></span> <span data-ttu-id="5fc71-187">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="5fc71-188">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="5fc71-189">Область действия</span><span class="sxs-lookup"><span data-stu-id="5fc71-189">Scoped</span></span>

<span data-ttu-id="5fc71-190">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="5fc71-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="5fc71-191">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="5fc71-192">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="5fc71-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="5fc71-193">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="5fc71-194">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-194">Singleton</span></span> <span data-ttu-id="5fc71-195">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="5fc71-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="5fc71-196">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-196">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="5fc71-197">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="5fc71-198">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="5fc71-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="5fc71-199">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="5fc71-199">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="5fc71-200">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="5fc71-201">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-201">Service registration methods</span></span> | <span data-ttu-id="5fc71-202">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="5fc71-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="5fc71-203">Метод</span><span class="sxs-lookup"><span data-stu-id="5fc71-203">Method</span></span> | <span data-ttu-id="5fc71-204">Автоматический</span><span class="sxs-lookup"><span data-stu-id="5fc71-204">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="5fc71-205">object</span><span class="sxs-lookup"><span data-stu-id="5fc71-205">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="5fc71-206">удаление</span><span class="sxs-lookup"><span data-stu-id="5fc71-206">disposal</span></span> | <span data-ttu-id="5fc71-207">Несколько</span><span class="sxs-lookup"><span data-stu-id="5fc71-207">Multiple</span></span> | <span data-ttu-id="5fc71-208">реализации</span><span class="sxs-lookup"><span data-stu-id="5fc71-208">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-209">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="5fc71-209">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="5fc71-210">Пример.</span><span class="sxs-lookup"><span data-stu-id="5fc71-210">Example:</span></span> | <span data-ttu-id="5fc71-211">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-211">Yes</span></span> | <span data-ttu-id="5fc71-212">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="5fc71-213">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-213">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="5fc71-214">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-214">Examples:</span></span> | <span data-ttu-id="5fc71-215">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-215">Yes</span></span> | <span data-ttu-id="5fc71-216">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-216">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-217">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-217">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="5fc71-218">Пример.</span><span class="sxs-lookup"><span data-stu-id="5fc71-218">Example:</span></span> | <span data-ttu-id="5fc71-219">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-219">Yes</span></span> | <span data-ttu-id="5fc71-220">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-220">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-221">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-221">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="5fc71-222">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-222">Examples:</span></span> | <span data-ttu-id="5fc71-223">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-223">No</span></span> | <span data-ttu-id="5fc71-224">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-224">Yes</span></span> |

<span data-ttu-id="5fc71-225">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-225">Yes</span></span> <span data-ttu-id="5fc71-226">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-226">Examples:</span></span>

<span data-ttu-id="5fc71-227">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-227">No</span></span>

<span data-ttu-id="5fc71-228">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-228">No</span></span> <span data-ttu-id="5fc71-229">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-229">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="5fc71-230">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="5fc71-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="5fc71-231">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="5fc71-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="5fc71-232">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="5fc71-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="5fc71-233">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="5fc71-234">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="5fc71-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="5fc71-235">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="5fc71-235">For more information, see:</span></span> <span data-ttu-id="5fc71-236">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="5fc71-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="5fc71-237">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="5fc71-238">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="5fc71-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="5fc71-239">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="5fc71-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="5fc71-240">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="5fc71-241">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-241">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="5fc71-242">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="5fc71-243">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="5fc71-243">Constructor injection behavior</span></span>

<span data-ttu-id="5fc71-244">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="5fc71-244">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="5fc71-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="5fc71-246">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="5fc71-247">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5fc71-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="5fc71-248">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="5fc71-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="5fc71-249">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="5fc71-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="5fc71-250">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="5fc71-251">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="5fc71-251">Entity Framework contexts</span></span> <span data-ttu-id="5fc71-252">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="5fc71-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="5fc71-253">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="5fc71-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="5fc71-254">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="5fc71-255">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="5fc71-255">Lifetime and registration options</span></span> <span data-ttu-id="5fc71-256">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="5fc71-257">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="5fc71-258">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="5fc71-259">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="5fc71-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="5fc71-260">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="5fc71-261">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="5fc71-262">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="5fc71-263">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="5fc71-264">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-264">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="5fc71-265">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="5fc71-266">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-266">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="5fc71-267">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="5fc71-268">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="5fc71-269">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="5fc71-270">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="5fc71-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="5fc71-271">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="5fc71-272">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="5fc71-273">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="5fc71-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="5fc71-274">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="5fc71-274">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="5fc71-275">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="5fc71-275">**First request:**</span></span>

<span data-ttu-id="5fc71-276">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="5fc71-276">Controller operations:</span></span>

<span data-ttu-id="5fc71-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="5fc71-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="5fc71-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5fc71-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5fc71-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5fc71-281">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-281">`OperationService` operations:</span></span>

<span data-ttu-id="5fc71-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="5fc71-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="5fc71-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5fc71-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5fc71-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-285">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="5fc71-286">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="5fc71-286">**Second request:**</span></span>

<span data-ttu-id="5fc71-287">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="5fc71-287">Controller operations:</span></span>

<span data-ttu-id="5fc71-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="5fc71-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="5fc71-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5fc71-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5fc71-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5fc71-292">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-292">`OperationService` operations:</span></span>

* <span data-ttu-id="5fc71-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="5fc71-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="5fc71-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5fc71-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="5fc71-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="5fc71-296">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="5fc71-297">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="5fc71-298">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="5fc71-298">*Transient* objects are always different.</span></span>

<span data-ttu-id="5fc71-299">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="5fc71-300">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5fc71-300">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="5fc71-301">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="5fc71-302">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="5fc71-303">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="5fc71-303">Call services from main</span></span>

* <span data-ttu-id="5fc71-304">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="5fc71-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="5fc71-305">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="5fc71-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="5fc71-306">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="5fc71-307">Проверка области</span><span class="sxs-lookup"><span data-stu-id="5fc71-307">Scope validation</span></span>

<span data-ttu-id="5fc71-308">Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="5fc71-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="5fc71-309">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="5fc71-310">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="5fc71-311">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="5fc71-312">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="5fc71-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5fc71-313">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="5fc71-313">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="5fc71-314">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="5fc71-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="5fc71-315">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5fc71-316">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="5fc71-317">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="5fc71-317">Request Services</span></span> <span data-ttu-id="5fc71-318">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="5fc71-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="5fc71-319">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-319">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="5fc71-320">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="5fc71-321">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="5fc71-321">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="5fc71-322">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span>
* <span data-ttu-id="5fc71-323">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="5fc71-323">This yields classes that are easier to test.</span></span> <span data-ttu-id="5fc71-324">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="5fc71-325">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-325">Design services for dependency injection</span></span> <span data-ttu-id="5fc71-326">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="5fc71-326">Best practices are to:</span></span>
* <span data-ttu-id="5fc71-327">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-327">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="5fc71-328">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="5fc71-329">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-329">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="5fc71-330">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="5fc71-331">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="5fc71-331">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="5fc71-332">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="5fc71-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="5fc71-333">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="5fc71-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="5fc71-334">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="5fc71-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="5fc71-335">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="5fc71-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="5fc71-336">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="5fc71-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="5fc71-337">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-337">Disposal of services</span></span>
* <span data-ttu-id="5fc71-338">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="5fc71-339">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="5fc71-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="5fc71-340">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="5fc71-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="5fc71-341">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="5fc71-341">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="5fc71-342">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-342">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="5fc71-343">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-343">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="5fc71-344">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="5fc71-344">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="5fc71-345">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="5fc71-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="5fc71-346">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="5fc71-346">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="5fc71-347">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-347">Transient, limited lifetime</span></span>

<span data-ttu-id="5fc71-348">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="5fc71-348">**Scenario**</span></span> <span data-ttu-id="5fc71-349">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="5fc71-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="5fc71-350">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-350">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="5fc71-351">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-351">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="5fc71-352">**Решение**</span><span class="sxs-lookup"><span data-stu-id="5fc71-352">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="5fc71-353">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-353">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="5fc71-354">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="5fc71-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="5fc71-355">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="5fc71-355">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="5fc71-356">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="5fc71-357">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="5fc71-358">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-358">Shared Instance, limited lifetime</span></span> <span data-ttu-id="5fc71-359">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="5fc71-359">**Scenario**</span></span> <span data-ttu-id="5fc71-360">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="5fc71-361">**Решение**</span><span class="sxs-lookup"><span data-stu-id="5fc71-361">**Solution**</span></span>

* <span data-ttu-id="5fc71-362">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="5fc71-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="5fc71-363">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="5fc71-364">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="5fc71-365">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-365">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="5fc71-366">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="5fc71-366">General Guidelines</span></span> <span data-ttu-id="5fc71-367">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="5fc71-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="5fc71-368">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="5fc71-368">Use the factory pattern instead.</span></span> <span data-ttu-id="5fc71-369">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="5fc71-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="5fc71-370">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="5fc71-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="5fc71-371">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="5fc71-372">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="5fc71-373">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-373">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="5fc71-374">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="5fc71-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="5fc71-375">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-375">Default service container replacement</span></span>
* <span data-ttu-id="5fc71-376">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="5fc71-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="5fc71-377">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="5fc71-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="5fc71-378">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="5fc71-378">Property injection</span></span>

<span data-ttu-id="5fc71-379">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="5fc71-379">Injection based on name</span></span>

* <span data-ttu-id="5fc71-380">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-380">Child containers</span></span>
* <span data-ttu-id="5fc71-381">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-381">Custom lifetime management</span></span>
* <span data-ttu-id="5fc71-382">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="5fc71-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="5fc71-383">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="5fc71-383">Convention-based registration</span></span>
* <span data-ttu-id="5fc71-384">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="5fc71-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* <span data-ttu-id="5fc71-385">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="5fc71-385">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="5fc71-386">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-386">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>

### <a name="thread-safety"></a><span data-ttu-id="5fc71-387">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="5fc71-387">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>

<span data-ttu-id="5fc71-388">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-388">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span> <span data-ttu-id="5fc71-389">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="5fc71-389">[Lamar](https://jasperfx.github.io/lamar/)</span></span>

<span data-ttu-id="5fc71-390">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-390">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span> [<span data-ttu-id="5fc71-391">Unity</span><span class="sxs-lookup"><span data-stu-id="5fc71-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="5fc71-392">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="5fc71-392">Thread safety</span></span>

* <span data-ttu-id="5fc71-393">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="5fc71-394">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="5fc71-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="5fc71-395">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="5fc71-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="5fc71-396">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="5fc71-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="5fc71-397">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="5fc71-397">Recommendations</span></span> <span data-ttu-id="5fc71-398">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5fc71-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="5fc71-399">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="5fc71-400">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-400">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="5fc71-401">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="5fc71-402">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5fc71-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

  <span data-ttu-id="5fc71-403">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="5fc71-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

  <span data-ttu-id="5fc71-404">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-404">It's better to request the actual item via DI.</span></span>

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

* <span data-ttu-id="5fc71-405">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span> <span data-ttu-id="5fc71-406">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="5fc71-406">Avoid using the *service locator pattern*.</span></span>

* <span data-ttu-id="5fc71-407">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="5fc71-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

<span data-ttu-id="5fc71-408">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="5fc71-408">**Incorrect:**</span></span> <span data-ttu-id="5fc71-409">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="5fc71-409">**Correct**:</span></span>

<span data-ttu-id="5fc71-410">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="5fc71-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="5fc71-411">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="5fc71-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="5fc71-412">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="5fc71-413">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="5fc71-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="5fc71-414">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="5fc71-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="5fc71-415">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-415">DI is an *alternative* to static/global object access patterns.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fc71-416">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="5fc71-417">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-417">Recommended patterns for multi-tenancy in DI</span></span>
* <span data-ttu-id="5fc71-418">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="5fc71-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span>
* <span data-ttu-id="5fc71-419">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="5fc71-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>
* <span data-ttu-id="5fc71-420">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="5fc71-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>
* <span data-ttu-id="5fc71-421">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5fc71-421">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[<span data-ttu-id="5fc71-422">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fc71-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[<span data-ttu-id="5fc71-423">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="5fc71-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)

[<span data-ttu-id="5fc71-424">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)

## <a name="overview-of-dependency-injection"></a>[<span data-ttu-id="5fc71-425">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="5fc71-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)

<span data-ttu-id="5fc71-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="5fc71-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span> <span data-ttu-id="5fc71-427">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="5fc71-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="5fc71-428">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="5fc71-429">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fc71-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="5fc71-430">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-430">Overview of dependency injection</span></span> <span data-ttu-id="5fc71-431">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="5fc71-431">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="5fc71-432">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="5fc71-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="5fc71-433">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="5fc71-434">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="5fc71-435">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="5fc71-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="5fc71-436">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="5fc71-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="5fc71-437">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="5fc71-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="5fc71-438">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="5fc71-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="5fc71-439">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="5fc71-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="5fc71-440">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="5fc71-441">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="5fc71-442">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5fc71-442">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="5fc71-443">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-443">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="5fc71-444">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-444">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="5fc71-445">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="5fc71-446">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5fc71-447">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="5fc71-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="5fc71-448">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="5fc71-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="5fc71-449">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="5fc71-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="5fc71-450">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="5fc71-451">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="5fc71-452">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="5fc71-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="5fc71-453">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-453">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="5fc71-454">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="5fc71-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="5fc71-455">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="5fc71-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="5fc71-456">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="5fc71-457">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="5fc71-458">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="5fc71-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="5fc71-459">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="5fc71-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="5fc71-460">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="5fc71-461">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="5fc71-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="5fc71-462">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="5fc71-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="5fc71-463">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="5fc71-464">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="5fc71-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="5fc71-465">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="5fc71-465">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="5fc71-466">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="5fc71-467">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5fc71-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="5fc71-468">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="5fc71-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="5fc71-469">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="5fc71-469">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5fc71-470">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="5fc71-471">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="5fc71-471">Services injected into Startup</span></span> <span data-ttu-id="5fc71-472">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="5fc71-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="5fc71-473">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-473">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="5fc71-474">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-474">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="5fc71-475">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-475">Framework-provided services</span></span> | <span data-ttu-id="5fc71-476">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5fc71-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-477">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="5fc71-478">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="5fc71-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="5fc71-479">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-479">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="5fc71-480">Тип службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-480">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="5fc71-481">Время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-481">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="5fc71-482">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-483">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-483">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="5fc71-484">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="5fc71-485">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="5fc71-486">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="5fc71-487">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="5fc71-488">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-488">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="5fc71-489">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="5fc71-490">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="5fc71-491">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-491">Singleton</span></span>

<span data-ttu-id="5fc71-492">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-492">Transient</span></span> <span data-ttu-id="5fc71-493">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-493">Singleton</span></span>

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

<span data-ttu-id="5fc71-494">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-494">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="5fc71-495">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-495">Singleton</span></span>

<span data-ttu-id="5fc71-496">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="5fc71-496">Register additional services with extension methods</span></span> <span data-ttu-id="5fc71-497">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="5fc71-498">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="5fc71-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="5fc71-499">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="5fc71-500">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-500">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="5fc71-501">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-501">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="5fc71-502">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="5fc71-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="5fc71-503">Временный</span><span class="sxs-lookup"><span data-stu-id="5fc71-503">Transient</span></span> <span data-ttu-id="5fc71-504">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="5fc71-505">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="5fc71-506">Область действия</span><span class="sxs-lookup"><span data-stu-id="5fc71-506">Scoped</span></span>

<span data-ttu-id="5fc71-507">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="5fc71-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="5fc71-508">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="5fc71-509">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="5fc71-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="5fc71-510">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="5fc71-511">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="5fc71-511">Singleton</span></span> <span data-ttu-id="5fc71-512">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="5fc71-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="5fc71-513">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-513">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="5fc71-514">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="5fc71-515">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="5fc71-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="5fc71-516">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="5fc71-516">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="5fc71-517">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="5fc71-518">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="5fc71-518">Service registration methods</span></span> | <span data-ttu-id="5fc71-519">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="5fc71-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="5fc71-520">Метод</span><span class="sxs-lookup"><span data-stu-id="5fc71-520">Method</span></span> | <span data-ttu-id="5fc71-521">Автоматический</span><span class="sxs-lookup"><span data-stu-id="5fc71-521">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="5fc71-522">object</span><span class="sxs-lookup"><span data-stu-id="5fc71-522">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="5fc71-523">удаление</span><span class="sxs-lookup"><span data-stu-id="5fc71-523">disposal</span></span> | <span data-ttu-id="5fc71-524">Несколько</span><span class="sxs-lookup"><span data-stu-id="5fc71-524">Multiple</span></span> | <span data-ttu-id="5fc71-525">реализации</span><span class="sxs-lookup"><span data-stu-id="5fc71-525">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-526">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="5fc71-526">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="5fc71-527">Пример.</span><span class="sxs-lookup"><span data-stu-id="5fc71-527">Example:</span></span> | <span data-ttu-id="5fc71-528">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-528">Yes</span></span> | <span data-ttu-id="5fc71-529">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="5fc71-530">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-530">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="5fc71-531">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-531">Examples:</span></span> | <span data-ttu-id="5fc71-532">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-532">Yes</span></span> | <span data-ttu-id="5fc71-533">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-533">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-534">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-534">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="5fc71-535">Пример.</span><span class="sxs-lookup"><span data-stu-id="5fc71-535">Example:</span></span> | <span data-ttu-id="5fc71-536">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-536">Yes</span></span> | <span data-ttu-id="5fc71-537">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-537">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="5fc71-538">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-538">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="5fc71-539">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-539">Examples:</span></span> | <span data-ttu-id="5fc71-540">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-540">No</span></span> | <span data-ttu-id="5fc71-541">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-541">Yes</span></span> |

<span data-ttu-id="5fc71-542">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-542">Yes</span></span> <span data-ttu-id="5fc71-543">Примеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-543">Examples:</span></span>

<span data-ttu-id="5fc71-544">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-544">No</span></span>

<span data-ttu-id="5fc71-545">Нет</span><span class="sxs-lookup"><span data-stu-id="5fc71-545">No</span></span> <span data-ttu-id="5fc71-546">Да</span><span class="sxs-lookup"><span data-stu-id="5fc71-546">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="5fc71-547">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="5fc71-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="5fc71-548">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="5fc71-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="5fc71-549">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="5fc71-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="5fc71-550">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="5fc71-551">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="5fc71-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="5fc71-552">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="5fc71-552">For more information, see:</span></span> <span data-ttu-id="5fc71-553">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="5fc71-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="5fc71-554">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="5fc71-555">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="5fc71-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="5fc71-556">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="5fc71-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="5fc71-557">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="5fc71-558">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-558">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="5fc71-559">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="5fc71-560">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="5fc71-560">Constructor injection behavior</span></span>

<span data-ttu-id="5fc71-561">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="5fc71-561">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="5fc71-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="5fc71-563">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="5fc71-564">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5fc71-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="5fc71-565">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="5fc71-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="5fc71-566">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="5fc71-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="5fc71-567">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="5fc71-568">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="5fc71-568">Entity Framework contexts</span></span> <span data-ttu-id="5fc71-569">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="5fc71-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="5fc71-570">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="5fc71-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="5fc71-571">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="5fc71-572">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="5fc71-572">Lifetime and registration options</span></span> <span data-ttu-id="5fc71-573">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="5fc71-574">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="5fc71-575">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="5fc71-576">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="5fc71-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="5fc71-577">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="5fc71-578">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="5fc71-579">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="5fc71-580">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="5fc71-581">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-581">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="5fc71-582">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="5fc71-583">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-583">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="5fc71-584">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="5fc71-585">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="5fc71-586">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="5fc71-587">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="5fc71-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="5fc71-588">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="5fc71-589">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="5fc71-590">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="5fc71-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="5fc71-591">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="5fc71-591">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="5fc71-592">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="5fc71-592">**First request:**</span></span>

<span data-ttu-id="5fc71-593">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="5fc71-593">Controller operations:</span></span>

<span data-ttu-id="5fc71-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="5fc71-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="5fc71-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5fc71-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5fc71-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-597">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5fc71-598">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-598">`OperationService` operations:</span></span>

<span data-ttu-id="5fc71-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="5fc71-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="5fc71-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5fc71-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5fc71-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-602">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="5fc71-603">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="5fc71-603">**Second request:**</span></span>

<span data-ttu-id="5fc71-604">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="5fc71-604">Controller operations:</span></span>

<span data-ttu-id="5fc71-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="5fc71-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="5fc71-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5fc71-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5fc71-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5fc71-608">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5fc71-609">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-609">`OperationService` operations:</span></span>

* <span data-ttu-id="5fc71-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="5fc71-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="5fc71-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5fc71-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="5fc71-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5fc71-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="5fc71-613">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5fc71-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="5fc71-614">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="5fc71-615">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="5fc71-615">*Transient* objects are always different.</span></span>

<span data-ttu-id="5fc71-616">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="5fc71-617">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="5fc71-617">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="5fc71-618">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="5fc71-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="5fc71-619">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="5fc71-620">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="5fc71-620">Call services from main</span></span>

* <span data-ttu-id="5fc71-621">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="5fc71-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="5fc71-622">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="5fc71-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="5fc71-623">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5fc71-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="5fc71-624">Проверка области</span><span class="sxs-lookup"><span data-stu-id="5fc71-624">Scope validation</span></span>

<span data-ttu-id="5fc71-625">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="5fc71-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="5fc71-626">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="5fc71-627">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="5fc71-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="5fc71-628">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="5fc71-629">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="5fc71-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5fc71-630">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="5fc71-630">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="5fc71-631">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="5fc71-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="5fc71-632">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5fc71-633">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="5fc71-634">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="5fc71-634">Request Services</span></span> <span data-ttu-id="5fc71-635">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="5fc71-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="5fc71-636">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-636">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="5fc71-637">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="5fc71-638">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="5fc71-638">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="5fc71-639">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span>
* <span data-ttu-id="5fc71-640">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="5fc71-640">This yields classes that are easier to test.</span></span> <span data-ttu-id="5fc71-641">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="5fc71-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="5fc71-642">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="5fc71-642">Design services for dependency injection</span></span> <span data-ttu-id="5fc71-643">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="5fc71-643">Best practices are to:</span></span>
* <span data-ttu-id="5fc71-644">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-644">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="5fc71-645">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="5fc71-646">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="5fc71-646">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="5fc71-647">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="5fc71-648">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="5fc71-648">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="5fc71-649">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="5fc71-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="5fc71-650">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="5fc71-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="5fc71-651">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="5fc71-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="5fc71-652">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="5fc71-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="5fc71-653">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="5fc71-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="5fc71-654">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-654">Disposal of services</span></span>
* <span data-ttu-id="5fc71-655">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="5fc71-656">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="5fc71-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="5fc71-657">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="5fc71-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="5fc71-658">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="5fc71-658">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="5fc71-659">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-659">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="5fc71-660">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-660">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="5fc71-661">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="5fc71-661">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="5fc71-662">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="5fc71-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="5fc71-663">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="5fc71-663">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="5fc71-664">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-664">Transient, limited lifetime</span></span>

<span data-ttu-id="5fc71-665">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="5fc71-665">**Scenario**</span></span> <span data-ttu-id="5fc71-666">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="5fc71-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="5fc71-667">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-667">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="5fc71-668">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-668">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="5fc71-669">**Решение**</span><span class="sxs-lookup"><span data-stu-id="5fc71-669">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="5fc71-670">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="5fc71-670">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="5fc71-671">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="5fc71-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="5fc71-672">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="5fc71-672">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="5fc71-673">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="5fc71-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="5fc71-674">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="5fc71-675">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-675">Shared Instance, limited lifetime</span></span> <span data-ttu-id="5fc71-676">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="5fc71-676">**Scenario**</span></span> <span data-ttu-id="5fc71-677">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="5fc71-678">**Решение**</span><span class="sxs-lookup"><span data-stu-id="5fc71-678">**Solution**</span></span>

* <span data-ttu-id="5fc71-679">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="5fc71-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="5fc71-680">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="5fc71-681">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="5fc71-682">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="5fc71-682">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="5fc71-683">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="5fc71-683">General Guidelines</span></span> <span data-ttu-id="5fc71-684">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="5fc71-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="5fc71-685">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="5fc71-685">Use the factory pattern instead.</span></span> <span data-ttu-id="5fc71-686">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="5fc71-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="5fc71-687">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="5fc71-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="5fc71-688">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="5fc71-689">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="5fc71-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="5fc71-690">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-690">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="5fc71-691">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="5fc71-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="5fc71-692">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="5fc71-692">Default service container replacement</span></span>
* <span data-ttu-id="5fc71-693">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="5fc71-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="5fc71-694">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="5fc71-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="5fc71-695">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="5fc71-695">Property injection</span></span>

<span data-ttu-id="5fc71-696">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="5fc71-696">Injection based on name</span></span>

* <span data-ttu-id="5fc71-697">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="5fc71-697">Child containers</span></span>
* <span data-ttu-id="5fc71-698">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="5fc71-698">Custom lifetime management</span></span>
* <span data-ttu-id="5fc71-699">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="5fc71-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="5fc71-700">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="5fc71-700">Convention-based registration</span></span>
* <span data-ttu-id="5fc71-701">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="5fc71-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* <span data-ttu-id="5fc71-702">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="5fc71-702">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="5fc71-703">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-703">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>

### <a name="thread-safety"></a><span data-ttu-id="5fc71-704">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="5fc71-704">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>

<span data-ttu-id="5fc71-705">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-705">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span> <span data-ttu-id="5fc71-706">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="5fc71-706">[Lamar](https://jasperfx.github.io/lamar/)</span></span>

<span data-ttu-id="5fc71-707">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="5fc71-707">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span> [<span data-ttu-id="5fc71-708">Unity</span><span class="sxs-lookup"><span data-stu-id="5fc71-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="5fc71-709">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="5fc71-709">Thread safety</span></span>

* <span data-ttu-id="5fc71-710">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="5fc71-711">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="5fc71-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="5fc71-712">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="5fc71-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="5fc71-713">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="5fc71-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="5fc71-714">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="5fc71-714">Recommendations</span></span> <span data-ttu-id="5fc71-715">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="5fc71-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="5fc71-716">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="5fc71-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="5fc71-717">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-717">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="5fc71-718">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="5fc71-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span>

  * <span data-ttu-id="5fc71-719">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5fc71-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

    <span data-ttu-id="5fc71-720">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="5fc71-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

    <span data-ttu-id="5fc71-721">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5fc71-721">It's better to request the actual item via DI.</span></span>

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

  * <span data-ttu-id="5fc71-722">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="5fc71-723">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="5fc71-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

<span data-ttu-id="5fc71-724">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="5fc71-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span> <span data-ttu-id="5fc71-725">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="5fc71-725">**Incorrect:**</span></span>

<span data-ttu-id="5fc71-726">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="5fc71-726">**Correct**:</span></span> <span data-ttu-id="5fc71-727">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="5fc71-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fc71-728">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="5fc71-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="5fc71-729">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="5fc71-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span>
* <span data-ttu-id="5fc71-730">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="5fc71-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>
* <span data-ttu-id="5fc71-731">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-731">DI is an *alternative* to static/global object access patterns.</span></span>
* <span data-ttu-id="5fc71-732">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="5fc71-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>
* <span data-ttu-id="5fc71-733">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5fc71-733">Additional resources</span></span>

::: moniker-end
