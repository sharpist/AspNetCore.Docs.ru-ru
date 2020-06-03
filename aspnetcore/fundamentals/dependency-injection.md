---
<span data-ttu-id="4699c-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-102">'Blazor'</span></span>
- <span data-ttu-id="4699c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-103">'Identity'</span></span>
- <span data-ttu-id="4699c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-105">'Razor'</span></span>
- <span data-ttu-id="4699c-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="4699c-107">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4699c-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="4699c-108">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith) и [Скотт Эдди](https://scottaddie.com) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="4699c-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4699c-109">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="4699c-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="4699c-110">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="4699c-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="4699c-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4699c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="4699c-112">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-112">Overview of dependency injection</span></span>

<span data-ttu-id="4699c-113">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="4699c-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="4699c-114">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="4699c-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="4699c-115">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="4699c-116">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="4699c-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="4699c-117">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="4699c-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="4699c-118">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="4699c-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="4699c-119">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="4699c-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="4699c-120">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="4699c-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="4699c-121">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="4699c-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="4699c-122">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="4699c-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="4699c-123">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="4699c-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="4699c-124">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="4699c-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="4699c-125">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="4699c-126">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="4699c-127">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="4699c-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="4699c-128">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="4699c-129">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="4699c-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="4699c-130">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="4699c-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="4699c-131">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="4699c-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="4699c-132">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="4699c-133">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="4699c-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="4699c-134">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="4699c-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="4699c-135">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="4699c-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="4699c-136">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="4699c-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="4699c-137">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="4699c-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="4699c-138">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="4699c-139">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4699c-140">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="4699c-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="4699c-141">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="4699c-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="4699c-142">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="4699c-143">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="4699c-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="4699c-144">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="4699c-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="4699c-145">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="4699c-146">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="4699c-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="4699c-147">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="4699c-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="4699c-148">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="4699c-149">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4699c-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="4699c-150">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="4699c-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="4699c-151">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="4699c-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="4699c-152">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="4699c-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="4699c-153">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="4699c-153">Services injected into Startup</span></span>

<span data-ttu-id="4699c-154">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="4699c-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="4699c-155">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4699c-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="4699c-156">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4699c-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4699c-157">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="4699c-157">Framework-provided services</span></span>

<span data-ttu-id="4699c-158">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4699c-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="4699c-159">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="4699c-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="4699c-160">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="4699c-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="4699c-161">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="4699c-162">Тип службы</span><span class="sxs-lookup"><span data-stu-id="4699c-162">Service Type</span></span> | <span data-ttu-id="4699c-163">Время существования</span><span class="sxs-lookup"><span data-stu-id="4699c-163">Lifetime</span></span> |
| ---
<span data-ttu-id="4699c-164">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-165">'Blazor'</span></span>
- <span data-ttu-id="4699c-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-166">'Identity'</span></span>
- <span data-ttu-id="4699c-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-168">'Razor'</span></span>
- <span data-ttu-id="4699c-169">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-170">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-171">'Blazor'</span></span>
- <span data-ttu-id="4699c-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-172">'Identity'</span></span>
- <span data-ttu-id="4699c-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-174">'Razor'</span></span>
- <span data-ttu-id="4699c-175">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-176">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-177">'Blazor'</span></span>
- <span data-ttu-id="4699c-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-178">'Identity'</span></span>
- <span data-ttu-id="4699c-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-180">'Razor'</span></span>
- <span data-ttu-id="4699c-181">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-182">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-183">'Blazor'</span></span>
- <span data-ttu-id="4699c-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-184">'Identity'</span></span>
- <span data-ttu-id="4699c-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-186">'Razor'</span></span>
- <span data-ttu-id="4699c-187">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-187">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-188">------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-189">'Blazor'</span></span>
- <span data-ttu-id="4699c-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-190">'Identity'</span></span>
- <span data-ttu-id="4699c-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-192">'Razor'</span></span>
- <span data-ttu-id="4699c-193">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-194">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-195">'Blazor'</span></span>
- <span data-ttu-id="4699c-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-196">'Identity'</span></span>
- <span data-ttu-id="4699c-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-198">'Razor'</span></span>
- <span data-ttu-id="4699c-199">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-199">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Временная | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Временная | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Временная | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Временная | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="4699c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="4699c-201">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="4699c-201">Register additional services with extension methods</span></span>

<span data-ttu-id="4699c-202">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="4699c-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="4699c-203">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="4699c-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="4699c-204">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="4699c-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="4699c-205">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="4699c-205">Service lifetimes</span></span>

<span data-ttu-id="4699c-206">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="4699c-207">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="4699c-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="4699c-208">Временный</span><span class="sxs-lookup"><span data-stu-id="4699c-208">Transient</span></span>

<span data-ttu-id="4699c-209">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="4699c-210">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="4699c-211">Область действия</span><span class="sxs-lookup"><span data-stu-id="4699c-211">Scoped</span></span>

<span data-ttu-id="4699c-212">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="4699c-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="4699c-213">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4699c-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="4699c-214">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="4699c-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="4699c-215">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="4699c-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="4699c-216">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="4699c-216">Singleton</span></span>

<span data-ttu-id="4699c-217">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="4699c-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="4699c-218">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="4699c-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="4699c-219">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="4699c-220">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="4699c-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="4699c-221">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="4699c-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="4699c-222">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="4699c-223">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="4699c-223">Service registration methods</span></span>

<span data-ttu-id="4699c-224">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="4699c-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="4699c-225">Метод</span><span class="sxs-lookup"><span data-stu-id="4699c-225">Method</span></span> | <span data-ttu-id="4699c-226">Автоматический</span><span class="sxs-lookup"><span data-stu-id="4699c-226">Automatic</span></span><br><span data-ttu-id="4699c-227">object</span><span class="sxs-lookup"><span data-stu-id="4699c-227">object</span></span><br><span data-ttu-id="4699c-228">удаление</span><span class="sxs-lookup"><span data-stu-id="4699c-228">disposal</span></span> | <span data-ttu-id="4699c-229">Несколько</span><span class="sxs-lookup"><span data-stu-id="4699c-229">Multiple</span></span><br><span data-ttu-id="4699c-230">реализации</span><span class="sxs-lookup"><span data-stu-id="4699c-230">implementations</span></span> | <span data-ttu-id="4699c-231">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="4699c-231">Pass args</span></span> |
| ---
<span data-ttu-id="4699c-232">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-233">'Blazor'</span></span>
- <span data-ttu-id="4699c-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-234">'Identity'</span></span>
- <span data-ttu-id="4699c-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-236">'Razor'</span></span>
- <span data-ttu-id="4699c-237">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-237">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-238">--- | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-239">'Blazor'</span></span>
- <span data-ttu-id="4699c-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-240">'Identity'</span></span>
- <span data-ttu-id="4699c-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-242">'Razor'</span></span>
- <span data-ttu-id="4699c-243">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-244">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-245">'Blazor'</span></span>
- <span data-ttu-id="4699c-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-246">'Identity'</span></span>
- <span data-ttu-id="4699c-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-248">'Razor'</span></span>
- <span data-ttu-id="4699c-249">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-250">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-251">'Blazor'</span></span>
- <span data-ttu-id="4699c-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-252">'Identity'</span></span>
- <span data-ttu-id="4699c-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-254">'Razor'</span></span>
- <span data-ttu-id="4699c-255">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-256">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-257">'Blazor'</span></span>
- <span data-ttu-id="4699c-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-258">'Identity'</span></span>
- <span data-ttu-id="4699c-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-260">'Razor'</span></span>
- <span data-ttu-id="4699c-261">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-262">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-263">'Blazor'</span></span>
- <span data-ttu-id="4699c-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-264">'Identity'</span></span>
- <span data-ttu-id="4699c-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-266">'Razor'</span></span>
- <span data-ttu-id="4699c-267">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-268">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-269">'Blazor'</span></span>
- <span data-ttu-id="4699c-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-270">'Identity'</span></span>
- <span data-ttu-id="4699c-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-272">'Razor'</span></span>
- <span data-ttu-id="4699c-273">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-274">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-275">'Blazor'</span></span>
- <span data-ttu-id="4699c-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-276">'Identity'</span></span>
- <span data-ttu-id="4699c-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-278">'Razor'</span></span>
- <span data-ttu-id="4699c-279">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-280">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-281">'Blazor'</span></span>
- <span data-ttu-id="4699c-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-282">'Identity'</span></span>
- <span data-ttu-id="4699c-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-284">'Razor'</span></span>
- <span data-ttu-id="4699c-285">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-286">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-287">'Blazor'</span></span>
- <span data-ttu-id="4699c-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-288">'Identity'</span></span>
- <span data-ttu-id="4699c-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-290">'Razor'</span></span>
- <span data-ttu-id="4699c-291">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-292">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-293">'Blazor'</span></span>
- <span data-ttu-id="4699c-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-294">'Identity'</span></span>
- <span data-ttu-id="4699c-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-296">'Razor'</span></span>
- <span data-ttu-id="4699c-297">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-298">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-299">'Blazor'</span></span>
- <span data-ttu-id="4699c-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-300">'Identity'</span></span>
- <span data-ttu-id="4699c-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-302">'Razor'</span></span>
- <span data-ttu-id="4699c-303">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-304">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-305">'Blazor'</span></span>
- <span data-ttu-id="4699c-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-306">'Identity'</span></span>
- <span data-ttu-id="4699c-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-308">'Razor'</span></span>
- <span data-ttu-id="4699c-309">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-309">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-310">---------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-311">'Blazor'</span></span>
- <span data-ttu-id="4699c-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-312">'Identity'</span></span>
- <span data-ttu-id="4699c-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-314">'Razor'</span></span>
- <span data-ttu-id="4699c-315">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-316">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-317">'Blazor'</span></span>
- <span data-ttu-id="4699c-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-318">'Identity'</span></span>
- <span data-ttu-id="4699c-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-320">'Razor'</span></span>
- <span data-ttu-id="4699c-321">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-322">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-323">'Blazor'</span></span>
- <span data-ttu-id="4699c-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-324">'Identity'</span></span>
- <span data-ttu-id="4699c-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-326">'Razor'</span></span>
- <span data-ttu-id="4699c-327">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-328">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-329">'Blazor'</span></span>
- <span data-ttu-id="4699c-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-330">'Identity'</span></span>
- <span data-ttu-id="4699c-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-332">'Razor'</span></span>
- <span data-ttu-id="4699c-333">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-334">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-335">'Blazor'</span></span>
- <span data-ttu-id="4699c-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-336">'Identity'</span></span>
- <span data-ttu-id="4699c-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-338">'Razor'</span></span>
- <span data-ttu-id="4699c-339">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-340">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-341">'Blazor'</span></span>
- <span data-ttu-id="4699c-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-342">'Identity'</span></span>
- <span data-ttu-id="4699c-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-344">'Razor'</span></span>
- <span data-ttu-id="4699c-345">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-346">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-347">'Blazor'</span></span>
- <span data-ttu-id="4699c-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-348">'Identity'</span></span>
- <span data-ttu-id="4699c-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-350">'Razor'</span></span>
- <span data-ttu-id="4699c-351">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-352">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-353">'Blazor'</span></span>
- <span data-ttu-id="4699c-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-354">'Identity'</span></span>
- <span data-ttu-id="4699c-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-356">'Razor'</span></span>
- <span data-ttu-id="4699c-357">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-358">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-359">'Blazor'</span></span>
- <span data-ttu-id="4699c-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-360">'Identity'</span></span>
- <span data-ttu-id="4699c-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-362">'Razor'</span></span>
- <span data-ttu-id="4699c-363">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-364">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-365">'Blazor'</span></span>
- <span data-ttu-id="4699c-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-366">'Identity'</span></span>
- <span data-ttu-id="4699c-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-368">'Razor'</span></span>
- <span data-ttu-id="4699c-369">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-369">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-370">-------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-371">'Blazor'</span></span>
- <span data-ttu-id="4699c-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-372">'Identity'</span></span>
- <span data-ttu-id="4699c-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-374">'Razor'</span></span>
- <span data-ttu-id="4699c-375">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-375">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="4699c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="4699c-377">Пример.</span><span class="sxs-lookup"><span data-stu-id="4699c-377">Example:</span></span><br><span data-ttu-id="4699c-378">`services.AddSingleton<IMyDep, MyDep>();` | Да | Да | Нет | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-379">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="4699c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Да | Да | Да | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="4699c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="4699c-381">Пример.</span><span class="sxs-lookup"><span data-stu-id="4699c-381">Example:</span></span><br><span data-ttu-id="4699c-382">`services.AddSingleton<MyDep>();` | Да | Нет | Нет | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-383">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="4699c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Нет | Да | Да | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-385">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="4699c-386">`services.AddSingleton(new MyDep("A string!"));` | Нет | Нет | Да |</span><span class="sxs-lookup"><span data-stu-id="4699c-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="4699c-387">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="4699c-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="4699c-388">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="4699c-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="4699c-389">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="4699c-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="4699c-390">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="4699c-391">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="4699c-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="4699c-392">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="4699c-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="4699c-393">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="4699c-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="4699c-394">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="4699c-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="4699c-395">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="4699c-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="4699c-396">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="4699c-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="4699c-397">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="4699c-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="4699c-398">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="4699c-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="4699c-399">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="4699c-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="4699c-400">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="4699c-400">Constructor injection behavior</span></span>

<span data-ttu-id="4699c-401">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="4699c-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="4699c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="4699c-403">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="4699c-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="4699c-404">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4699c-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="4699c-405">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="4699c-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="4699c-406">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="4699c-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="4699c-407">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="4699c-408">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="4699c-408">Entity Framework contexts</span></span>

<span data-ttu-id="4699c-409">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="4699c-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="4699c-410">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="4699c-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="4699c-411">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="4699c-412">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="4699c-412">Lifetime and registration options</span></span>

<span data-ttu-id="4699c-413">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="4699c-414">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="4699c-415">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="4699c-416">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="4699c-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="4699c-417">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="4699c-418">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="4699c-419">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="4699c-420">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="4699c-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="4699c-421">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="4699c-422">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="4699c-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="4699c-423">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="4699c-424">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="4699c-425">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="4699c-426">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="4699c-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="4699c-427">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="4699c-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="4699c-428">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="4699c-429">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="4699c-430">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="4699c-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="4699c-431">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="4699c-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="4699c-432">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="4699c-432">**First request:**</span></span>

<span data-ttu-id="4699c-433">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="4699c-433">Controller operations:</span></span>

<span data-ttu-id="4699c-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="4699c-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="4699c-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4699c-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4699c-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-437">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-438">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="4699c-438">`OperationService` operations:</span></span>

<span data-ttu-id="4699c-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="4699c-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="4699c-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4699c-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4699c-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-442">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-443">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="4699c-443">**Second request:**</span></span>

<span data-ttu-id="4699c-444">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="4699c-444">Controller operations:</span></span>

<span data-ttu-id="4699c-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="4699c-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="4699c-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4699c-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4699c-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-448">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-449">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="4699c-449">`OperationService` operations:</span></span>

<span data-ttu-id="4699c-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="4699c-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="4699c-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4699c-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4699c-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-453">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-454">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="4699c-455">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="4699c-455">*Transient* objects are always different.</span></span> <span data-ttu-id="4699c-456">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="4699c-457">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="4699c-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="4699c-458">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="4699c-459">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="4699c-460">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="4699c-460">Call services from main</span></span>

<span data-ttu-id="4699c-461">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="4699c-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="4699c-462">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="4699c-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="4699c-463">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4699c-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="4699c-464">Проверка области</span><span class="sxs-lookup"><span data-stu-id="4699c-464">Scope validation</span></span>

<span data-ttu-id="4699c-465">Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="4699c-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4699c-466">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="4699c-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4699c-467">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="4699c-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4699c-468">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="4699c-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="4699c-469">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="4699c-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4699c-470">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="4699c-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4699c-471">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="4699c-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4699c-472">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="4699c-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4699c-473">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="4699c-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="4699c-474">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="4699c-474">Request Services</span></span>

<span data-ttu-id="4699c-475">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="4699c-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="4699c-476">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="4699c-477">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="4699c-478">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="4699c-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="4699c-479">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="4699c-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="4699c-480">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="4699c-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="4699c-481">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="4699c-482">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-482">Design services for dependency injection</span></span>

<span data-ttu-id="4699c-483">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="4699c-483">Best practices are to:</span></span>

* <span data-ttu-id="4699c-484">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="4699c-485">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="4699c-486">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="4699c-487">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="4699c-488">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="4699c-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="4699c-489">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="4699c-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="4699c-490">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="4699c-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="4699c-491">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="4699c-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="4699c-492">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="4699c-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="4699c-493">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="4699c-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="4699c-494">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="4699c-494">Disposal of services</span></span>

<span data-ttu-id="4699c-495">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="4699c-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="4699c-496">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="4699c-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="4699c-497">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="4699c-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="4699c-498">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="4699c-498">In the following example:</span></span>

* <span data-ttu-id="4699c-499">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="4699c-500">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="4699c-501">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="4699c-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="4699c-502">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4699c-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="4699c-503">Сведения о вариантах удаления служб см. в разделе [Четыре способа удаления IDisposable в ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="4699c-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="4699c-504">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="4699c-504">Default service container replacement</span></span>

<span data-ttu-id="4699c-505">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="4699c-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="4699c-506">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="4699c-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="4699c-507">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="4699c-507">Property injection</span></span>
* <span data-ttu-id="4699c-508">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="4699c-508">Injection based on name</span></span>
* <span data-ttu-id="4699c-509">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="4699c-509">Child containers</span></span>
* <span data-ttu-id="4699c-510">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="4699c-510">Custom lifetime management</span></span>
* <span data-ttu-id="4699c-511">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="4699c-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="4699c-512">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="4699c-512">Convention-based registration</span></span>

<span data-ttu-id="4699c-513">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="4699c-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="4699c-514">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="4699c-514">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="4699c-515">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-515">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="4699c-516">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="4699c-516">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="4699c-517">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-517">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="4699c-518">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="4699c-518">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="4699c-519">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-519">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="4699c-520">Unity</span><span class="sxs-lookup"><span data-stu-id="4699c-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="4699c-521">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="4699c-521">Thread safety</span></span>

<span data-ttu-id="4699c-522">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="4699c-523">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="4699c-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="4699c-524">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="4699c-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="4699c-525">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="4699c-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4699c-526">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="4699c-526">Recommendations</span></span>

* <span data-ttu-id="4699c-527">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="4699c-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="4699c-528">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="4699c-529">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="4699c-530">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="4699c-531">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4699c-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4699c-532">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="4699c-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="4699c-533">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="4699c-534">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="4699c-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="4699c-535">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="4699c-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="4699c-536">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="4699c-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="4699c-537">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="4699c-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="4699c-538">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="4699c-538">**Correct**:</span></span>

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

* <span data-ttu-id="4699c-539">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="4699c-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="4699c-540">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="4699c-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="4699c-541">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="4699c-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="4699c-542">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="4699c-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="4699c-543">Такие исключения случаются редко. Главным образом они связаны с особенностями самой платформы.</span><span class="sxs-lookup"><span data-stu-id="4699c-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="4699c-544">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="4699c-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="4699c-545">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="4699c-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="4699c-546">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="4699c-547">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="4699c-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="4699c-548">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="4699c-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="4699c-549">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="4699c-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4699c-550">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4699c-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="4699c-551">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="4699c-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="4699c-552">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="4699c-553">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="4699c-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="4699c-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4699c-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4699c-555">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="4699c-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="4699c-556">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="4699c-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="4699c-557">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4699c-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="4699c-558">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-558">Overview of dependency injection</span></span>

<span data-ttu-id="4699c-559">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="4699c-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="4699c-560">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="4699c-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="4699c-561">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="4699c-562">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="4699c-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="4699c-563">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="4699c-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="4699c-564">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="4699c-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="4699c-565">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="4699c-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="4699c-566">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="4699c-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="4699c-567">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="4699c-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="4699c-568">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="4699c-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="4699c-569">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="4699c-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="4699c-570">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="4699c-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="4699c-571">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="4699c-572">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="4699c-573">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="4699c-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="4699c-574">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="4699c-575">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="4699c-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="4699c-576">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="4699c-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="4699c-577">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="4699c-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="4699c-578">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="4699c-579">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="4699c-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="4699c-580">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="4699c-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="4699c-581">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="4699c-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="4699c-582">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="4699c-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="4699c-583">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="4699c-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="4699c-584">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="4699c-585">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4699c-586">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="4699c-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="4699c-587">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="4699c-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="4699c-588">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="4699c-589">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="4699c-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="4699c-590">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="4699c-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="4699c-591">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="4699c-592">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="4699c-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="4699c-593">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="4699c-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="4699c-594">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="4699c-595">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4699c-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="4699c-596">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="4699c-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="4699c-597">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="4699c-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="4699c-598">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="4699c-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="4699c-599">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="4699c-599">Services injected into Startup</span></span>

<span data-ttu-id="4699c-600">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="4699c-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="4699c-601">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4699c-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="4699c-602">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4699c-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4699c-603">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="4699c-603">Framework-provided services</span></span>

<span data-ttu-id="4699c-604">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4699c-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="4699c-605">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="4699c-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="4699c-606">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="4699c-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="4699c-607">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="4699c-608">Тип службы</span><span class="sxs-lookup"><span data-stu-id="4699c-608">Service Type</span></span> | <span data-ttu-id="4699c-609">Время существования</span><span class="sxs-lookup"><span data-stu-id="4699c-609">Lifetime</span></span> |
| ---
<span data-ttu-id="4699c-610">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-611">'Blazor'</span></span>
- <span data-ttu-id="4699c-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-612">'Identity'</span></span>
- <span data-ttu-id="4699c-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-614">'Razor'</span></span>
- <span data-ttu-id="4699c-615">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-616">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-617">'Blazor'</span></span>
- <span data-ttu-id="4699c-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-618">'Identity'</span></span>
- <span data-ttu-id="4699c-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-620">'Razor'</span></span>
- <span data-ttu-id="4699c-621">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-622">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-623">'Blazor'</span></span>
- <span data-ttu-id="4699c-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-624">'Identity'</span></span>
- <span data-ttu-id="4699c-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-626">'Razor'</span></span>
- <span data-ttu-id="4699c-627">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-628">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-629">'Blazor'</span></span>
- <span data-ttu-id="4699c-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-630">'Identity'</span></span>
- <span data-ttu-id="4699c-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-632">'Razor'</span></span>
- <span data-ttu-id="4699c-633">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-633">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-634">------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-635">'Blazor'</span></span>
- <span data-ttu-id="4699c-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-636">'Identity'</span></span>
- <span data-ttu-id="4699c-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-638">'Razor'</span></span>
- <span data-ttu-id="4699c-639">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-640">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-641">'Blazor'</span></span>
- <span data-ttu-id="4699c-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-642">'Identity'</span></span>
- <span data-ttu-id="4699c-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-644">'Razor'</span></span>
- <span data-ttu-id="4699c-645">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-645">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Временная | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Временная | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Временная | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Временная | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="4699c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="4699c-647">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="4699c-647">Register additional services with extension methods</span></span>

<span data-ttu-id="4699c-648">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="4699c-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="4699c-649">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="4699c-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="4699c-650">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="4699c-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="4699c-651">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="4699c-651">Service lifetimes</span></span>

<span data-ttu-id="4699c-652">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="4699c-653">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="4699c-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="4699c-654">Временный</span><span class="sxs-lookup"><span data-stu-id="4699c-654">Transient</span></span>

<span data-ttu-id="4699c-655">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="4699c-656">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="4699c-657">Область действия</span><span class="sxs-lookup"><span data-stu-id="4699c-657">Scoped</span></span>

<span data-ttu-id="4699c-658">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="4699c-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="4699c-659">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4699c-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="4699c-660">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="4699c-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="4699c-661">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="4699c-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="4699c-662">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="4699c-662">Singleton</span></span>

<span data-ttu-id="4699c-663">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="4699c-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="4699c-664">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="4699c-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="4699c-665">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="4699c-666">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="4699c-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="4699c-667">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="4699c-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="4699c-668">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="4699c-669">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="4699c-669">Service registration methods</span></span>

<span data-ttu-id="4699c-670">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="4699c-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="4699c-671">Метод</span><span class="sxs-lookup"><span data-stu-id="4699c-671">Method</span></span> | <span data-ttu-id="4699c-672">Автоматический</span><span class="sxs-lookup"><span data-stu-id="4699c-672">Automatic</span></span><br><span data-ttu-id="4699c-673">object</span><span class="sxs-lookup"><span data-stu-id="4699c-673">object</span></span><br><span data-ttu-id="4699c-674">удаление</span><span class="sxs-lookup"><span data-stu-id="4699c-674">disposal</span></span> | <span data-ttu-id="4699c-675">Несколько</span><span class="sxs-lookup"><span data-stu-id="4699c-675">Multiple</span></span><br><span data-ttu-id="4699c-676">реализации</span><span class="sxs-lookup"><span data-stu-id="4699c-676">implementations</span></span> | <span data-ttu-id="4699c-677">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="4699c-677">Pass args</span></span> |
| ---
<span data-ttu-id="4699c-678">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-679">'Blazor'</span></span>
- <span data-ttu-id="4699c-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-680">'Identity'</span></span>
- <span data-ttu-id="4699c-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-682">'Razor'</span></span>
- <span data-ttu-id="4699c-683">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-683">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-684">--- | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-685">'Blazor'</span></span>
- <span data-ttu-id="4699c-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-686">'Identity'</span></span>
- <span data-ttu-id="4699c-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-688">'Razor'</span></span>
- <span data-ttu-id="4699c-689">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-690">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-691">'Blazor'</span></span>
- <span data-ttu-id="4699c-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-692">'Identity'</span></span>
- <span data-ttu-id="4699c-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-694">'Razor'</span></span>
- <span data-ttu-id="4699c-695">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-696">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-697">'Blazor'</span></span>
- <span data-ttu-id="4699c-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-698">'Identity'</span></span>
- <span data-ttu-id="4699c-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-700">'Razor'</span></span>
- <span data-ttu-id="4699c-701">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-702">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-703">'Blazor'</span></span>
- <span data-ttu-id="4699c-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-704">'Identity'</span></span>
- <span data-ttu-id="4699c-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-706">'Razor'</span></span>
- <span data-ttu-id="4699c-707">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-708">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-709">'Blazor'</span></span>
- <span data-ttu-id="4699c-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-710">'Identity'</span></span>
- <span data-ttu-id="4699c-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-712">'Razor'</span></span>
- <span data-ttu-id="4699c-713">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-714">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-715">'Blazor'</span></span>
- <span data-ttu-id="4699c-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-716">'Identity'</span></span>
- <span data-ttu-id="4699c-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-718">'Razor'</span></span>
- <span data-ttu-id="4699c-719">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-720">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-721">'Blazor'</span></span>
- <span data-ttu-id="4699c-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-722">'Identity'</span></span>
- <span data-ttu-id="4699c-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-724">'Razor'</span></span>
- <span data-ttu-id="4699c-725">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-726">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-727">'Blazor'</span></span>
- <span data-ttu-id="4699c-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-728">'Identity'</span></span>
- <span data-ttu-id="4699c-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-730">'Razor'</span></span>
- <span data-ttu-id="4699c-731">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-732">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-733">'Blazor'</span></span>
- <span data-ttu-id="4699c-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-734">'Identity'</span></span>
- <span data-ttu-id="4699c-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-736">'Razor'</span></span>
- <span data-ttu-id="4699c-737">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-738">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-739">'Blazor'</span></span>
- <span data-ttu-id="4699c-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-740">'Identity'</span></span>
- <span data-ttu-id="4699c-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-742">'Razor'</span></span>
- <span data-ttu-id="4699c-743">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-744">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-745">'Blazor'</span></span>
- <span data-ttu-id="4699c-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-746">'Identity'</span></span>
- <span data-ttu-id="4699c-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-748">'Razor'</span></span>
- <span data-ttu-id="4699c-749">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-750">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-751">'Blazor'</span></span>
- <span data-ttu-id="4699c-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-752">'Identity'</span></span>
- <span data-ttu-id="4699c-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-754">'Razor'</span></span>
- <span data-ttu-id="4699c-755">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-755">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-756">---------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-757">'Blazor'</span></span>
- <span data-ttu-id="4699c-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-758">'Identity'</span></span>
- <span data-ttu-id="4699c-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-760">'Razor'</span></span>
- <span data-ttu-id="4699c-761">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-762">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-763">'Blazor'</span></span>
- <span data-ttu-id="4699c-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-764">'Identity'</span></span>
- <span data-ttu-id="4699c-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-766">'Razor'</span></span>
- <span data-ttu-id="4699c-767">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-768">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-769">'Blazor'</span></span>
- <span data-ttu-id="4699c-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-770">'Identity'</span></span>
- <span data-ttu-id="4699c-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-772">'Razor'</span></span>
- <span data-ttu-id="4699c-773">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-774">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-775">'Blazor'</span></span>
- <span data-ttu-id="4699c-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-776">'Identity'</span></span>
- <span data-ttu-id="4699c-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-778">'Razor'</span></span>
- <span data-ttu-id="4699c-779">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-780">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-781">'Blazor'</span></span>
- <span data-ttu-id="4699c-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-782">'Identity'</span></span>
- <span data-ttu-id="4699c-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-784">'Razor'</span></span>
- <span data-ttu-id="4699c-785">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-786">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-787">'Blazor'</span></span>
- <span data-ttu-id="4699c-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-788">'Identity'</span></span>
- <span data-ttu-id="4699c-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-790">'Razor'</span></span>
- <span data-ttu-id="4699c-791">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-792">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-793">'Blazor'</span></span>
- <span data-ttu-id="4699c-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-794">'Identity'</span></span>
- <span data-ttu-id="4699c-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-796">'Razor'</span></span>
- <span data-ttu-id="4699c-797">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-798">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-799">'Blazor'</span></span>
- <span data-ttu-id="4699c-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-800">'Identity'</span></span>
- <span data-ttu-id="4699c-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-802">'Razor'</span></span>
- <span data-ttu-id="4699c-803">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-804">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-805">'Blazor'</span></span>
- <span data-ttu-id="4699c-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-806">'Identity'</span></span>
- <span data-ttu-id="4699c-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-808">'Razor'</span></span>
- <span data-ttu-id="4699c-809">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4699c-810">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-811">'Blazor'</span></span>
- <span data-ttu-id="4699c-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-812">'Identity'</span></span>
- <span data-ttu-id="4699c-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-814">'Razor'</span></span>
- <span data-ttu-id="4699c-815">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-815">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-816">-------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4699c-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4699c-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4699c-817">'Blazor'</span></span>
- <span data-ttu-id="4699c-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4699c-818">'Identity'</span></span>
- <span data-ttu-id="4699c-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4699c-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="4699c-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4699c-820">'Razor'</span></span>
- <span data-ttu-id="4699c-821">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4699c-821">'SignalR' uid:</span></span> 

<span data-ttu-id="4699c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="4699c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="4699c-823">Пример.</span><span class="sxs-lookup"><span data-stu-id="4699c-823">Example:</span></span><br><span data-ttu-id="4699c-824">`services.AddSingleton<IMyDep, MyDep>();` | Да | Да | Нет | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-825">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="4699c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Да | Да | Да | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="4699c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="4699c-827">Пример.</span><span class="sxs-lookup"><span data-stu-id="4699c-827">Example:</span></span><br><span data-ttu-id="4699c-828">`services.AddSingleton<MyDep>();` | Да | Нет | Нет | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-829">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="4699c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Нет | Да | Да | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="4699c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="4699c-831">Примеры</span><span class="sxs-lookup"><span data-stu-id="4699c-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="4699c-832">`services.AddSingleton(new MyDep("A string!"));` | Нет | Нет | Да |</span><span class="sxs-lookup"><span data-stu-id="4699c-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="4699c-833">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="4699c-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="4699c-834">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="4699c-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="4699c-835">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="4699c-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="4699c-836">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="4699c-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="4699c-837">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="4699c-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="4699c-838">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="4699c-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="4699c-839">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="4699c-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="4699c-840">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="4699c-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="4699c-841">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="4699c-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="4699c-842">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="4699c-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="4699c-843">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="4699c-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="4699c-844">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="4699c-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="4699c-845">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="4699c-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="4699c-846">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="4699c-846">Constructor injection behavior</span></span>

<span data-ttu-id="4699c-847">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="4699c-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="4699c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="4699c-849">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="4699c-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="4699c-850">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4699c-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="4699c-851">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="4699c-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="4699c-852">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="4699c-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="4699c-853">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="4699c-854">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="4699c-854">Entity Framework contexts</span></span>

<span data-ttu-id="4699c-855">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="4699c-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="4699c-856">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="4699c-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="4699c-857">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="4699c-858">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="4699c-858">Lifetime and registration options</span></span>

<span data-ttu-id="4699c-859">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="4699c-860">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="4699c-861">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="4699c-862">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="4699c-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="4699c-863">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="4699c-864">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="4699c-865">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="4699c-866">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="4699c-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="4699c-867">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="4699c-868">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="4699c-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="4699c-869">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="4699c-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="4699c-870">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="4699c-871">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="4699c-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="4699c-872">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="4699c-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="4699c-873">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="4699c-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="4699c-874">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="4699c-875">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="4699c-876">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="4699c-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="4699c-877">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="4699c-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="4699c-878">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="4699c-878">**First request:**</span></span>

<span data-ttu-id="4699c-879">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="4699c-879">Controller operations:</span></span>

<span data-ttu-id="4699c-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="4699c-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="4699c-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4699c-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4699c-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-883">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-884">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="4699c-884">`OperationService` operations:</span></span>

<span data-ttu-id="4699c-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="4699c-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="4699c-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4699c-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4699c-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-888">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-889">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="4699c-889">**Second request:**</span></span>

<span data-ttu-id="4699c-890">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="4699c-890">Controller operations:</span></span>

<span data-ttu-id="4699c-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="4699c-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="4699c-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4699c-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4699c-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-894">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-895">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="4699c-895">`OperationService` operations:</span></span>

<span data-ttu-id="4699c-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="4699c-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="4699c-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4699c-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4699c-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4699c-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4699c-899">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4699c-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4699c-900">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="4699c-901">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="4699c-901">*Transient* objects are always different.</span></span> <span data-ttu-id="4699c-902">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="4699c-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="4699c-903">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="4699c-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="4699c-904">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="4699c-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="4699c-905">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="4699c-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="4699c-906">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="4699c-906">Call services from main</span></span>

<span data-ttu-id="4699c-907">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="4699c-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="4699c-908">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="4699c-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="4699c-909">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4699c-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="4699c-910">Проверка области</span><span class="sxs-lookup"><span data-stu-id="4699c-910">Scope validation</span></span>

<span data-ttu-id="4699c-911">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="4699c-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4699c-912">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="4699c-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4699c-913">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="4699c-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4699c-914">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="4699c-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="4699c-915">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="4699c-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4699c-916">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="4699c-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4699c-917">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="4699c-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4699c-918">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="4699c-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4699c-919">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="4699c-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="4699c-920">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="4699c-920">Request Services</span></span>

<span data-ttu-id="4699c-921">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="4699c-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="4699c-922">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="4699c-923">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="4699c-924">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="4699c-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="4699c-925">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="4699c-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="4699c-926">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="4699c-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="4699c-927">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="4699c-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="4699c-928">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-928">Design services for dependency injection</span></span>

<span data-ttu-id="4699c-929">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="4699c-929">Best practices are to:</span></span>

* <span data-ttu-id="4699c-930">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="4699c-931">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="4699c-932">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="4699c-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="4699c-933">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="4699c-934">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="4699c-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="4699c-935">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="4699c-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="4699c-936">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="4699c-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="4699c-937">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="4699c-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="4699c-938">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="4699c-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="4699c-939">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="4699c-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="4699c-940">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="4699c-940">Disposal of services</span></span>

<span data-ttu-id="4699c-941">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="4699c-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="4699c-942">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="4699c-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="4699c-943">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="4699c-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="4699c-944">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="4699c-944">In the following example:</span></span>

* <span data-ttu-id="4699c-945">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="4699c-946">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="4699c-947">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="4699c-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="4699c-948">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4699c-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="4699c-949">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="4699c-949">Default service container replacement</span></span>

<span data-ttu-id="4699c-950">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="4699c-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="4699c-951">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="4699c-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="4699c-952">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="4699c-952">Property injection</span></span>
* <span data-ttu-id="4699c-953">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="4699c-953">Injection based on name</span></span>
* <span data-ttu-id="4699c-954">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="4699c-954">Child containers</span></span>
* <span data-ttu-id="4699c-955">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="4699c-955">Custom lifetime management</span></span>
* <span data-ttu-id="4699c-956">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="4699c-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="4699c-957">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="4699c-957">Convention-based registration</span></span>

<span data-ttu-id="4699c-958">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="4699c-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="4699c-959">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="4699c-959">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="4699c-960">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-960">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="4699c-961">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="4699c-961">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="4699c-962">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-962">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="4699c-963">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="4699c-963">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="4699c-964">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="4699c-964">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="4699c-965">Unity</span><span class="sxs-lookup"><span data-stu-id="4699c-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="4699c-966">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="4699c-966">Thread safety</span></span>

<span data-ttu-id="4699c-967">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="4699c-968">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="4699c-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="4699c-969">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="4699c-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="4699c-970">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="4699c-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4699c-971">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="4699c-971">Recommendations</span></span>

* <span data-ttu-id="4699c-972">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="4699c-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="4699c-973">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="4699c-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="4699c-974">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="4699c-975">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="4699c-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="4699c-976">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4699c-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4699c-977">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="4699c-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="4699c-978">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="4699c-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="4699c-979">Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).</span><span class="sxs-lookup"><span data-stu-id="4699c-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="4699c-980">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="4699c-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="4699c-981">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="4699c-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="4699c-982">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="4699c-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="4699c-983">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="4699c-983">**Correct**:</span></span>

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

  * <span data-ttu-id="4699c-984">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="4699c-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="4699c-985">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="4699c-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="4699c-986">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="4699c-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="4699c-987">Такие исключения случаются редко. Главным образом они связаны с особенностями самой платформы.</span><span class="sxs-lookup"><span data-stu-id="4699c-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="4699c-988">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="4699c-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="4699c-989">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="4699c-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4699c-990">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4699c-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="4699c-991">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="4699c-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="4699c-992">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="4699c-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="4699c-993">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="4699c-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="4699c-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4699c-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
