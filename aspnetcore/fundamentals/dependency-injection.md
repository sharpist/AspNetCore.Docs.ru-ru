---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628798"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0ae1e-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ae1e-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0ae1e-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0ae1e-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0ae1e-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0ae1e-107">Дополнительные сведения о внедрении параметров зависимостей см. в разделе <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0ae1e-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ae1e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0ae1e-109">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-109">Overview of dependency injection</span></span>

<span data-ttu-id="0ae1e-110">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0ae1e-111">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="0ae1e-112">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0ae1e-113">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="0ae1e-114">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0ae1e-115">Зависимости в коде, как в предыдущем примере, представляют собой определенную проблему. Их следует избегать по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0ae1e-116">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0ae1e-117">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0ae1e-118">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0ae1e-119">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0ae1e-120">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0ae1e-121">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0ae1e-122">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0ae1e-123">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0ae1e-124">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0ae1e-125">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0ae1e-126">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0ae1e-127">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0ae1e-128">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0ae1e-129">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0ae1e-130">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0ae1e-131">Метод <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> регистрирует службу с заданной областью времени существования, временем существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="0ae1e-132">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="0ae1e-133">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="0ae1e-134">С шаблоном DI:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-134">With the DI pattern:</span></span>

* <span data-ttu-id="0ae1e-135">Контроллер не использует конкретный тип `MyDependency`, только `IMyDependency` интерфейса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="0ae1e-136">Это упрощает изменение реализации, используемой контроллером, без изменения контроллера.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="0ae1e-137">Контроллер не создает экземпляр `MyDependency`, он создается контейнером DI.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="0ae1e-138">Реализацию интерфейса `IMyDependency` можно улучшить с помощью встроенного API ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="0ae1e-139">Обновленный метод `ConfigureServices` регистрирует новую реализацию `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="0ae1e-140">`MyDependency2` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="0ae1e-141">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0ae1e-142">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0ae1e-143">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0ae1e-144">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0ae1e-145">`ILogger<TCategoryName>` — это [предоставленная платформой служба](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="0ae1e-146">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="0ae1e-147">В терминологии внедрения зависимостей — служба:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="0ae1e-148">Обычно является объектом, предоставляющим службу для другого кода в приложении, например службу `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="0ae1e-149">Не относится к веб-службе, хотя служба может использовать веб-службу.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="0ae1e-150">Платформа предоставляет эффективную систему [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="0ae1e-151">Реализации `IMyDependency` были написаны для демонстрации базового DI, а не для реализации ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="0ae1e-152">Большинству приложений не нужно писать средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="0ae1e-153">В следующем коде показано использование журнала по умолчанию, для которого не требуется регистрация служб в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="0ae1e-154">Используя приведенный выше код, не нужно обновлять `ConfigureServices`, поскольку платформа не предоставляет возможность [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="0ae1e-155">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="0ae1e-155">Services injected into Startup</span></span>

<span data-ttu-id="0ae1e-156">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0ae1e-157">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0ae1e-158">Дополнительные сведения см. в разделах <xref:fundamentals/startup> и [Доступ к конфигурации во время запуска](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0ae1e-159">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="0ae1e-159">Register additional services with extension methods</span></span>

<span data-ttu-id="0ae1e-160">Если для регистрации службы доступен метод расширения коллекции служб:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="0ae1e-161">Соглашение заключается в использовании одного метода расширения `Add{SERVICE_NAME}` для регистрации всех служб, необходимых для этой службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="0ae1e-162">Зависимые службы также регистрируются.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-162">The dependent services are also registered.</span></span>

<span data-ttu-id="0ae1e-163">Следующий код создается шаблоном Razor Pages с использованием отдельных учетных записей пользователей. Он демонстрирует, как добавить дополнительные службы в контейнер с помощью методов расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="0ae1e-164">Дополнительные сведения см. в разделах <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> и <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="0ae1e-165">Инструкции по написанию метода расширения для регистрации служб см. в разделе [Объединение коллекций служб](#csc).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="0ae1e-166">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-166">Service lifetimes</span></span>

<span data-ttu-id="0ae1e-167">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0ae1e-168">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0ae1e-169">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-169">Transient</span></span>

<span data-ttu-id="0ae1e-170">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0ae1e-171">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="0ae1e-172">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0ae1e-173">Область действия</span><span class="sxs-lookup"><span data-stu-id="0ae1e-173">Scoped</span></span>

<span data-ttu-id="0ae1e-174">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="0ae1e-175">При использовании Entity Framework Core метод расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> по умолчанию регистрирует типы `DbContext` с заданной областью времени существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="0ae1e-176">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="0ae1e-177">Используйте службы с заданной областью ПО промежуточного слоя, используя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="0ae1e-178">Вставьте службу в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0ae1e-179">Внедрение с помощью [внедрения конструктора](xref:mvc/controllers/dependency-injection#constructor-injection) создает исключение во время выполнения, поскольку оно заставляет службу вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0ae1e-180">В примере в разделе [Параметры времени существования и регистрации](#lifetime-and-registration-options) используется подход `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="0ae1e-181">[Фабричное ПО промежуточного слоя](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="0ae1e-182">Методы расширения <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> проверяют, реализует ли зарегистрированный тип ПО промежуточного слоя <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="0ae1e-183">Если да, то экземпляр <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, зарегистрированный в контейнере, используется для разрешения реализации <xref:Microsoft.AspNetCore.Http.IMiddleware> вместо стандартной логики активации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="0ae1e-184">ПО промежуточного слоя регистрируется как ограниченная или временная служба в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="0ae1e-185">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="0ae1e-186">Разрешать службу с заданной областью из одноэлементного объекта ***запрещено***.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0ae1e-187">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0ae1e-188">Допускается следующее:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-188">It's fine to:</span></span>

* <span data-ttu-id="0ae1e-189">Разрешение одноэлементной службы из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="0ae1e-190">Разрешение службы с заданной областью из другой службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="0ae1e-191">По умолчанию в среде разработки разрешение службы из другой службы с более длинным временем существования вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="0ae1e-192">Дополнительные сведения см. в разделе [Проверка области](#sv).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="0ae1e-193">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-193">Singleton</span></span>

<span data-ttu-id="0ae1e-194">Одноэлементные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="0ae1e-195">При первом запросе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-195">The first time they're requested.</span></span>
* <span data-ttu-id="0ae1e-196">Разработчиком при предоставлении экземпляра реализации непосредственно в контейнер.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="0ae1e-197">Этот подход требуется достаточно редко.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-197">This approach is rarely needed.</span></span>

<span data-ttu-id="0ae1e-198">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0ae1e-199">Если в приложении нужно использовать одноэлементные службы, разрешите контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="0ae1e-200">Не реализуйте одноэлементный подход и предоставьте код для удаления одноэлементных объектов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="0ae1e-201">Службы никогда не должны удаляться кодом, который разрешил службу из контейнера.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="0ae1e-202">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер будет автоматически удалять одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="0ae1e-203">Одноэлементные службы должны быть потокобезопасными и часто использоваться в службах без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="0ae1e-204">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="0ae1e-205">Поскольку память не освобождается до завершения работы приложения, необходимо учитывать использование памяти одноэлементным объектом.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="0ae1e-206">Разрешать службу с заданной областью из одноэлементного объекта ***запрещено***.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0ae1e-207">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0ae1e-208">Допускается разрешать одноэлементную службу из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0ae1e-209">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-209">Service registration methods</span></span>

<span data-ttu-id="0ae1e-210">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="0ae1e-211">Метод</span><span class="sxs-lookup"><span data-stu-id="0ae1e-211">Method</span></span> | <span data-ttu-id="0ae1e-212">Автоматически</span><span class="sxs-lookup"><span data-stu-id="0ae1e-212">Automatic</span></span><br><span data-ttu-id="0ae1e-213">object</span><span class="sxs-lookup"><span data-stu-id="0ae1e-213">object</span></span><br><span data-ttu-id="0ae1e-214">удаление</span><span class="sxs-lookup"><span data-stu-id="0ae1e-214">disposal</span></span> | <span data-ttu-id="0ae1e-215">Несколько</span><span class="sxs-lookup"><span data-stu-id="0ae1e-215">Multiple</span></span><br><span data-ttu-id="0ae1e-216">реализации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-216">implementations</span></span> | <span data-ttu-id="0ae1e-217">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="0ae1e-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0ae1e-218">Пример</span><span class="sxs-lookup"><span data-stu-id="0ae1e-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0ae1e-219">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-219">Yes</span></span> | <span data-ttu-id="0ae1e-220">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-220">Yes</span></span> | <span data-ttu-id="0ae1e-221">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-222">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="0ae1e-223">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-223">Yes</span></span> | <span data-ttu-id="0ae1e-224">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-224">Yes</span></span> | <span data-ttu-id="0ae1e-225">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0ae1e-226">Пример</span><span class="sxs-lookup"><span data-stu-id="0ae1e-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0ae1e-227">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-227">Yes</span></span> | <span data-ttu-id="0ae1e-228">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-228">No</span></span> | <span data-ttu-id="0ae1e-229">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-230">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="0ae1e-231">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-231">No</span></span> | <span data-ttu-id="0ae1e-232">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-232">Yes</span></span> | <span data-ttu-id="0ae1e-233">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-234">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="0ae1e-235">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-235">No</span></span> | <span data-ttu-id="0ae1e-236">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-236">No</span></span> | <span data-ttu-id="0ae1e-237">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-237">Yes</span></span> |

<span data-ttu-id="0ae1e-238">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0ae1e-239">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0ae1e-240">Методы `TryAdd{LIFETIME}` регистрируют службу в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0ae1e-241">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0ae1e-242">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0ae1e-243">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0ae1e-244">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0ae1e-245">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0ae1e-246">При регистрации служб разработчику необходимо добавить экземпляр в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0ae1e-247">Как правило, авторы библиотек используют `TryAddEnumerable`, чтобы избежать регистрации нескольких копий реализации в контейнере.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="0ae1e-248">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0ae1e-249">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0ae1e-250">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="0ae1e-251">Регистрация службы обычно не зависит от порядка, за исключением случаев регистрации нескольких реализаций одного типа.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="0ae1e-252">`IServiceCollection` — это коллекция <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="0ae1e-253">В следующем коде показано, как добавить службу с помощью конструктора:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="0ae1e-254">Методы `Add{LIFETIME}` используют аналогичный подход.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="0ae1e-255">Например, см. [исходный код для AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0ae1e-256">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="0ae1e-256">Constructor injection behavior</span></span>

<span data-ttu-id="0ae1e-257">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0ae1e-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="0ae1e-259">создает объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="0ae1e-260">Используется с функциями платформы, такими как [вспомогательные приложения для тегов](xref:mvc/views/tag-helpers/intro), контроллеры MVC и [средства привязки моделей](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="0ae1e-261">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0ae1e-262">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0ae1e-263">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0ae1e-264">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0ae1e-265">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="0ae1e-265">Entity Framework contexts</span></span>

<span data-ttu-id="0ae1e-266">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0ae1e-267">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0ae1e-268">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0ae1e-269">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-269">Lifetime and registration options</span></span>

<span data-ttu-id="0ae1e-270">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="0ae1e-271">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0ae1e-272">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0ae1e-273">Конструктор `Operation` создает последние 4 символа GUID, если он не указан:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="0ae1e-274">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="0ae1e-275">В примере приложения показано время существования объектов между запросами.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="0ae1e-276">В примере приложения `IndexModel` и ПО промежуточного слоя запрашивает каждый вид типа `IOperation` и регистрирует `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0ae1e-277">ПО промежуточного слоя аналогично `IndexModel` и разрешает те же службы:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="0ae1e-278">Службы с заданной областью должны быть разрешены в методе `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="0ae1e-279">Выходные данные средства ведения журнала содержат:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-279">The logger output shows:</span></span>

* <span data-ttu-id="0ae1e-280">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-280">*Transient* objects are always different.</span></span> <span data-ttu-id="0ae1e-281">Значение промежуточного `OperationId` отличается в `IndexModel` и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="0ae1e-282">Объекты с *заданной областью* остаются неизменными в пределах одного запроса, но в разных запросах используются разные объекты.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="0ae1e-283">*Одноэлементные* объекты одинаковы для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="0ae1e-284">Чтобы уменьшить объем выводимых данных журнала, задайте в файле *appsettings.Development.json* параметр "Logging:LogLevel:Microsoft:Error".</span><span class="sxs-lookup"><span data-stu-id="0ae1e-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="0ae1e-285">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="0ae1e-285">Call services from main</span></span>

<span data-ttu-id="0ae1e-286">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0ae1e-287">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="0ae1e-288">Приведенный выше код взят из раздела [Добавление инициализатора заполнения](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) учебника по Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="0ae1e-289">В следующем примере показано, как получить контекст для `IMyDependency` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="0ae1e-290">Проверка области</span><span class="sxs-lookup"><span data-stu-id="0ae1e-290">Scope validation</span></span>

<span data-ttu-id="0ae1e-291">Когда приложение выполняется в [среде разработки](xref:fundamentals/environments) и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0ae1e-292">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0ae1e-293">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="0ae1e-294">Временные службы не вводятся в одноэлементные объекты или службы с заданной областью, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="0ae1e-295">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0ae1e-296">Время существования корневого поставщика службы соответствует времени существования приложения — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0ae1e-297">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0ae1e-298">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="0ae1e-299">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0ae1e-300">Дополнительные сведения см. в разделе [Проверка области](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="0ae1e-301">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="0ae1e-301">Request Services</span></span>

<span data-ttu-id="0ae1e-302">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0ae1e-303">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0ae1e-304">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0ae1e-305">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0ae1e-306">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0ae1e-307">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="0ae1e-308">ASP.NET Core создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="0ae1e-309">Все службы с заданной областью действительны до тех пор, пока запрос активен.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="0ae1e-310">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0ae1e-311">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-311">Design services for dependency injection</span></span>

<span data-ttu-id="0ae1e-312">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-312">Best practices are to:</span></span>

* <span data-ttu-id="0ae1e-313">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0ae1e-314">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0ae1e-315">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0ae1e-316">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0ae1e-317">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0ae1e-318">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0ae1e-319">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0ae1e-320">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0ae1e-321">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0ae1e-322">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-322">Disposal of services</span></span>

<span data-ttu-id="0ae1e-323">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0ae1e-324">Службы никогда не должны удаляться кодом, который разрешил службу из контейнера.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="0ae1e-325">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="0ae1e-326">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0ae1e-327">После каждого обновления страницы индекса в консоли отладки отображаются следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="0ae1e-328">Службы, не созданные контейнером службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="0ae1e-329">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="0ae1e-330">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-330">In the preceding code:</span></span>

* <span data-ttu-id="0ae1e-331">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0ae1e-332">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0ae1e-333">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0ae1e-334">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0ae1e-335">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="0ae1e-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0ae1e-336">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-336">Transient, limited lifetime</span></span>

<span data-ttu-id="0ae1e-337">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-337">**Scenario**</span></span>

<span data-ttu-id="0ae1e-338">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0ae1e-339">Экземпляр разрешается в корневой области (в корневом контейнере).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="0ae1e-340">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0ae1e-341">**Решение**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-341">**Solution**</span></span>

<span data-ttu-id="0ae1e-342">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0ae1e-343">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0ae1e-344">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0ae1e-345">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0ae1e-346">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0ae1e-347">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0ae1e-348">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-348">**Scenario**</span></span>

<span data-ttu-id="0ae1e-349">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0ae1e-350">**Решение**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-350">**Solution**</span></span>

<span data-ttu-id="0ae1e-351">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0ae1e-352">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0ae1e-353">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0ae1e-354">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="0ae1e-355">Общие рекомендации по IDisposable</span><span class="sxs-lookup"><span data-stu-id="0ae1e-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="0ae1e-356">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0ae1e-357">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0ae1e-358">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0ae1e-359">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0ae1e-360">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0ae1e-361">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0ae1e-362">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0ae1e-363">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0ae1e-364">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-364">Default service container replacement</span></span>

<span data-ttu-id="0ae1e-365">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0ae1e-366">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0ae1e-367">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="0ae1e-367">Property injection</span></span>
* <span data-ttu-id="0ae1e-368">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="0ae1e-368">Injection based on name</span></span>
* <span data-ttu-id="0ae1e-369">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="0ae1e-369">Child containers</span></span>
* <span data-ttu-id="0ae1e-370">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-370">Custom lifetime management</span></span>
* <span data-ttu-id="0ae1e-371">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="0ae1e-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0ae1e-372">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="0ae1e-372">Convention-based registration</span></span>

<span data-ttu-id="0ae1e-373">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="0ae1e-374">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-374">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="0ae1e-375">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-375">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="0ae1e-376">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-376">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="0ae1e-377">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-377">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="0ae1e-378">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-378">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="0ae1e-379">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-379">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="0ae1e-380">Unity</span><span class="sxs-lookup"><span data-stu-id="0ae1e-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0ae1e-381">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="0ae1e-381">Thread safety</span></span>

<span data-ttu-id="0ae1e-382">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="0ae1e-383">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0ae1e-384">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0ae1e-385">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0ae1e-386">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-386">Recommendations</span></span>

* <span data-ttu-id="0ae1e-387">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0ae1e-388">C# не поддерживает асинхронные конструкторы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="0ae1e-389">Рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="0ae1e-390">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0ae1e-391">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0ae1e-392">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0ae1e-393">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0ae1e-394">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0ae1e-395">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-395">Avoid static access to services.</span></span> <span data-ttu-id="0ae1e-396">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="0ae1e-397">Обеспечьте быстродействие и синхронизацию фабрик DI.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="0ae1e-398">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0ae1e-399">Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0ae1e-400">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-400">**Incorrect:**</span></span>

    ![Неверный код](dependency-injection/_static/bad.png)

  <span data-ttu-id="0ae1e-402">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-402">**Correct**:</span></span>

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
* <span data-ttu-id="0ae1e-403">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0ae1e-404">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="0ae1e-405">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="0ae1e-406">Избегайте вызовов <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="0ae1e-407">Вызов `BuildServiceProvider` обычно происходит, когда разработчику необходимо разрешить службу в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="0ae1e-408">Например, рассмотрим случай, когда необходимо получить `LoginPath` из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="0ae1e-409">Добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-409">Avoid the following code:</span></span>

  ![Неверный код при вызове BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="0ae1e-411">На предыдущем рисунке при выборе строки, отмеченной зеленой волнистой линией в разделе `services.BuildServiceProvider`, отображается следующее предупреждение ASP0000:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="0ae1e-412">ASP0000. Вызов BuildServiceProvider из кода приложения приводит к созданию дополнительной копии создаваемых одноэлементных служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="0ae1e-413">В качестве параметров для Configure можно использовать альтернативные варианты, такие как службы внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="0ae1e-414">При вызове `BuildServiceProvider` создается второй контейнер, который может создавать разорванные одноэлементные экземпляры и ссылаться на графы объектов в нескольких контейнерах.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="0ae1e-415">Правильный способ получения `LoginPath` — использование шаблона параметра с DI:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="0ae1e-416">Неудаляемые временные службы фиксируются контейнером для их удаления.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="0ae1e-417">Это может привести к утечке памяти, если она разрешена из контейнера верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="0ae1e-418">Включите проверку области, чтобы убедиться, что приложение не имеет служб с заданной областью, записывающих одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="0ae1e-419">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="0ae1e-420">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0ae1e-421">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0ae1e-422">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0ae1e-423">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0ae1e-424">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0ae1e-425">Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="0ae1e-426">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0ae1e-427">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0ae1e-428">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-428">Framework-provided services</span></span>

<span data-ttu-id="0ae1e-429">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0ae1e-430">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0ae1e-431">Для приложений, основанных на шаблонах ASP.NET Core, платформой зарегистрировано свыше 250 служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="0ae1e-432">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0ae1e-433">Тип службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-433">Service Type</span></span> | <span data-ttu-id="0ae1e-434">Время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-435">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="0ae1e-436">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="0ae1e-437">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0ae1e-438">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0ae1e-439">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0ae1e-440">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-441">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-442">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-443">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0ae1e-444">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-445">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-446">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0ae1e-447">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0ae1e-448">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0ae1e-449">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="0ae1e-450">Шаблоны конференций NDC для разработки приложений с внедрением зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0ae1e-451">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ae1e-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0ae1e-452">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0ae1e-453">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0ae1e-454">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="0ae1e-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0ae1e-456">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0ae1e-457">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0ae1e-458">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0ae1e-459">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ae1e-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0ae1e-460">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-460">Overview of dependency injection</span></span>

<span data-ttu-id="0ae1e-461">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0ae1e-462">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0ae1e-463">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0ae1e-464">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0ae1e-465">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0ae1e-466">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0ae1e-467">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0ae1e-468">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0ae1e-469">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0ae1e-470">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0ae1e-471">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0ae1e-472">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0ae1e-473">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0ae1e-474">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0ae1e-475">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0ae1e-476">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0ae1e-477">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0ae1e-478">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0ae1e-479">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0ae1e-480">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0ae1e-481">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0ae1e-482">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0ae1e-483">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0ae1e-484">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0ae1e-485">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0ae1e-486">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0ae1e-487">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0ae1e-488">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0ae1e-489">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0ae1e-490">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0ae1e-491">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0ae1e-492">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0ae1e-493">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0ae1e-494">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0ae1e-495">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0ae1e-496">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0ae1e-497">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0ae1e-498">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0ae1e-499">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0ae1e-500">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0ae1e-501">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="0ae1e-501">Services injected into Startup</span></span>

<span data-ttu-id="0ae1e-502">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0ae1e-503">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0ae1e-504">Дополнительные сведения см. в разделе <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0ae1e-505">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-505">Framework-provided services</span></span>

<span data-ttu-id="0ae1e-506">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0ae1e-507">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0ae1e-508">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0ae1e-509">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0ae1e-510">Тип службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-510">Service Type</span></span> | <span data-ttu-id="0ae1e-511">Время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-512">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0ae1e-513">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0ae1e-514">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0ae1e-515">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0ae1e-516">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0ae1e-517">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-518">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-519">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0ae1e-520">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0ae1e-521">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-522">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0ae1e-523">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0ae1e-524">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0ae1e-525">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0ae1e-526">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="0ae1e-526">Register additional services with extension methods</span></span>

<span data-ttu-id="0ae1e-527">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0ae1e-528">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0ae1e-529">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0ae1e-530">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-530">Service lifetimes</span></span>

<span data-ttu-id="0ae1e-531">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0ae1e-532">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0ae1e-533">Временный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-533">Transient</span></span>

<span data-ttu-id="0ae1e-534">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0ae1e-535">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="0ae1e-536">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0ae1e-537">Область действия</span><span class="sxs-lookup"><span data-stu-id="0ae1e-537">Scoped</span></span>

<span data-ttu-id="0ae1e-538">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="0ae1e-539">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="0ae1e-540">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0ae1e-541">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0ae1e-542">Дополнительные сведения см. в разделе <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0ae1e-543">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="0ae1e-543">Singleton</span></span>

<span data-ttu-id="0ae1e-544">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0ae1e-545">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0ae1e-546">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0ae1e-547">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="0ae1e-548">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="0ae1e-549">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0ae1e-550">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0ae1e-551">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-551">Service registration methods</span></span>

<span data-ttu-id="0ae1e-552">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0ae1e-553">Метод</span><span class="sxs-lookup"><span data-stu-id="0ae1e-553">Method</span></span> | <span data-ttu-id="0ae1e-554">Автоматически</span><span class="sxs-lookup"><span data-stu-id="0ae1e-554">Automatic</span></span><br><span data-ttu-id="0ae1e-555">объект</span><span class="sxs-lookup"><span data-stu-id="0ae1e-555">object</span></span><br><span data-ttu-id="0ae1e-556">удаление</span><span class="sxs-lookup"><span data-stu-id="0ae1e-556">disposal</span></span> | <span data-ttu-id="0ae1e-557">Несколько</span><span class="sxs-lookup"><span data-stu-id="0ae1e-557">Multiple</span></span><br><span data-ttu-id="0ae1e-558">реализации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-558">implementations</span></span> | <span data-ttu-id="0ae1e-559">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="0ae1e-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0ae1e-560">Пример</span><span class="sxs-lookup"><span data-stu-id="0ae1e-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0ae1e-561">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-561">Yes</span></span> | <span data-ttu-id="0ae1e-562">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-562">Yes</span></span> | <span data-ttu-id="0ae1e-563">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-564">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0ae1e-565">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-565">Yes</span></span> | <span data-ttu-id="0ae1e-566">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-566">Yes</span></span> | <span data-ttu-id="0ae1e-567">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0ae1e-568">Пример</span><span class="sxs-lookup"><span data-stu-id="0ae1e-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0ae1e-569">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-569">Yes</span></span> | <span data-ttu-id="0ae1e-570">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-570">No</span></span> | <span data-ttu-id="0ae1e-571">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-572">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0ae1e-573">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-573">No</span></span> | <span data-ttu-id="0ae1e-574">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-574">Yes</span></span> | <span data-ttu-id="0ae1e-575">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0ae1e-576">Примеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0ae1e-577">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-577">No</span></span> | <span data-ttu-id="0ae1e-578">Нет</span><span class="sxs-lookup"><span data-stu-id="0ae1e-578">No</span></span> | <span data-ttu-id="0ae1e-579">Да</span><span class="sxs-lookup"><span data-stu-id="0ae1e-579">Yes</span></span> |

<span data-ttu-id="0ae1e-580">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0ae1e-581">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0ae1e-582">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0ae1e-583">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0ae1e-584">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0ae1e-585">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0ae1e-586">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0ae1e-587">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0ae1e-588">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0ae1e-589">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0ae1e-590">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0ae1e-591">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0ae1e-592">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0ae1e-593">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="0ae1e-593">Constructor injection behavior</span></span>

<span data-ttu-id="0ae1e-594">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0ae1e-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0ae1e-596">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0ae1e-597">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0ae1e-598">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0ae1e-599">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0ae1e-600">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0ae1e-601">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="0ae1e-601">Entity Framework contexts</span></span>

<span data-ttu-id="0ae1e-602">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0ae1e-603">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0ae1e-604">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0ae1e-605">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-605">Lifetime and registration options</span></span>

<span data-ttu-id="0ae1e-606">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0ae1e-607">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0ae1e-608">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0ae1e-609">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0ae1e-610">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0ae1e-611">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0ae1e-612">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0ae1e-613">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0ae1e-614">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0ae1e-615">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0ae1e-616">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0ae1e-617">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0ae1e-618">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0ae1e-619">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0ae1e-620">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0ae1e-621">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0ae1e-622">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0ae1e-623">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0ae1e-624">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0ae1e-625">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-625">**First request:**</span></span>

<span data-ttu-id="0ae1e-626">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-626">Controller operations:</span></span>

<span data-ttu-id="0ae1e-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0ae1e-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0ae1e-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0ae1e-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0ae1e-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0ae1e-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0ae1e-630">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0ae1e-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0ae1e-631">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-631">`OperationService` operations:</span></span>

<span data-ttu-id="0ae1e-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0ae1e-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0ae1e-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0ae1e-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0ae1e-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0ae1e-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0ae1e-635">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0ae1e-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0ae1e-636">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-636">**Second request:**</span></span>

<span data-ttu-id="0ae1e-637">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-637">Controller operations:</span></span>

<span data-ttu-id="0ae1e-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0ae1e-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0ae1e-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0ae1e-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0ae1e-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0ae1e-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0ae1e-641">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0ae1e-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0ae1e-642">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-642">`OperationService` operations:</span></span>

<span data-ttu-id="0ae1e-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0ae1e-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0ae1e-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0ae1e-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0ae1e-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0ae1e-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0ae1e-646">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0ae1e-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0ae1e-647">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0ae1e-648">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-648">*Transient* objects are always different.</span></span> <span data-ttu-id="0ae1e-649">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0ae1e-650">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0ae1e-651">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0ae1e-652">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0ae1e-653">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="0ae1e-653">Call services from main</span></span>

<span data-ttu-id="0ae1e-654">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0ae1e-655">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0ae1e-656">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0ae1e-657">Проверка области</span><span class="sxs-lookup"><span data-stu-id="0ae1e-657">Scope validation</span></span>

<span data-ttu-id="0ae1e-658">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0ae1e-659">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0ae1e-660">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0ae1e-661">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0ae1e-662">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0ae1e-663">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0ae1e-664">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0ae1e-665">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0ae1e-666">Дополнительные сведения см. в разделе <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="0ae1e-667">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="0ae1e-667">Request Services</span></span>

<span data-ttu-id="0ae1e-668">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0ae1e-669">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0ae1e-670">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0ae1e-671">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0ae1e-672">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0ae1e-673">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0ae1e-674">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0ae1e-675">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-675">Design services for dependency injection</span></span>

<span data-ttu-id="0ae1e-676">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-676">Best practices are to:</span></span>

* <span data-ttu-id="0ae1e-677">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0ae1e-678">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0ae1e-679">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0ae1e-680">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0ae1e-681">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0ae1e-682">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0ae1e-683">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0ae1e-684">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0ae1e-685">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0ae1e-686">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0ae1e-687">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-687">Disposal of services</span></span>

<span data-ttu-id="0ae1e-688">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0ae1e-689">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0ae1e-690">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0ae1e-691">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-691">In the following example:</span></span>

* <span data-ttu-id="0ae1e-692">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0ae1e-693">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0ae1e-694">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0ae1e-695">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0ae1e-696">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="0ae1e-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0ae1e-697">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-697">Transient, limited lifetime</span></span>

<span data-ttu-id="0ae1e-698">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-698">**Scenario**</span></span>

<span data-ttu-id="0ae1e-699">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0ae1e-700">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0ae1e-701">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0ae1e-702">**Решение**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-702">**Solution**</span></span>

<span data-ttu-id="0ae1e-703">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0ae1e-704">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0ae1e-705">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0ae1e-706">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0ae1e-707">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0ae1e-708">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0ae1e-709">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-709">**Scenario**</span></span>

<span data-ttu-id="0ae1e-710">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0ae1e-711">**Решение**</span><span class="sxs-lookup"><span data-stu-id="0ae1e-711">**Solution**</span></span>

<span data-ttu-id="0ae1e-712">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0ae1e-713">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0ae1e-714">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0ae1e-715">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0ae1e-716">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-716">General Guidelines</span></span>

* <span data-ttu-id="0ae1e-717">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0ae1e-718">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0ae1e-719">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0ae1e-720">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0ae1e-721">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0ae1e-722">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0ae1e-723">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0ae1e-724">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0ae1e-725">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="0ae1e-725">Default service container replacement</span></span>

<span data-ttu-id="0ae1e-726">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0ae1e-727">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0ae1e-728">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="0ae1e-728">Property injection</span></span>
* <span data-ttu-id="0ae1e-729">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="0ae1e-729">Injection based on name</span></span>
* <span data-ttu-id="0ae1e-730">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="0ae1e-730">Child containers</span></span>
* <span data-ttu-id="0ae1e-731">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="0ae1e-731">Custom lifetime management</span></span>
* <span data-ttu-id="0ae1e-732">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="0ae1e-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0ae1e-733">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="0ae1e-733">Convention-based registration</span></span>

<span data-ttu-id="0ae1e-734">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="0ae1e-735">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-735">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="0ae1e-736">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-736">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="0ae1e-737">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-737">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="0ae1e-738">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-738">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="0ae1e-739">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-739">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="0ae1e-740">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="0ae1e-740">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="0ae1e-741">Unity</span><span class="sxs-lookup"><span data-stu-id="0ae1e-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0ae1e-742">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="0ae1e-742">Thread safety</span></span>

<span data-ttu-id="0ae1e-743">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="0ae1e-744">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0ae1e-745">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0ae1e-746">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0ae1e-747">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="0ae1e-747">Recommendations</span></span>

* <span data-ttu-id="0ae1e-748">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0ae1e-749">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0ae1e-750">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0ae1e-751">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0ae1e-752">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0ae1e-753">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0ae1e-754">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0ae1e-755">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-755">Avoid static access to services.</span></span> <span data-ttu-id="0ae1e-756">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="0ae1e-757">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="0ae1e-758">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0ae1e-759">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-759">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="0ae1e-760">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="0ae1e-760">**Correct**:</span></span>

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

* <span data-ttu-id="0ae1e-761">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="0ae1e-762">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0ae1e-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0ae1e-763">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0ae1e-764">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0ae1e-765">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0ae1e-766">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="0ae1e-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ae1e-767">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0ae1e-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0ae1e-768">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ae1e-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0ae1e-769">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0ae1e-770">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="0ae1e-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0ae1e-771">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="0ae1e-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="0ae1e-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
