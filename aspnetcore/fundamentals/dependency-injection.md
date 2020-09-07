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
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153549"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="de17f-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de17f-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="de17f-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="de17f-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="de17f-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="de17f-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="de17f-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="de17f-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="de17f-107">Дополнительные сведения о внедрении параметров зависимостей см. в разделе <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="de17f-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="de17f-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de17f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="de17f-109">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-109">Overview of dependency injection</span></span>

<span data-ttu-id="de17f-110">*Зависимость* — это любой объект, от которого зависит другой объект.</span><span class="sxs-lookup"><span data-stu-id="de17f-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="de17f-111">Рассмотрим следующий класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы:</span><span class="sxs-lookup"><span data-stu-id="de17f-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="de17f-112">Класс может создать экземпляр класса `MyDependency`, чтобы использовать его метод `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="de17f-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="de17f-113">В следующем примере класс `MyDependency` выступает зависимостью класса `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="de17f-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="de17f-114">Этот класс создает `MyDependency` и напрямую зависит от этого класса.</span><span class="sxs-lookup"><span data-stu-id="de17f-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="de17f-115">Зависимости в коде, как в предыдущем примере, представляют собой определенную проблему. Их следует избегать по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="de17f-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="de17f-116">Чтобы заменить `MyDependency` другой реализацией, класс `IndexModel` необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="de17f-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="de17f-117">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="de17f-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="de17f-118">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="de17f-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="de17f-119">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="de17f-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="de17f-120">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="de17f-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="de17f-121">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="de17f-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="de17f-122">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="de17f-123">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="de17f-124">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="de17f-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="de17f-125">Как правило, службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="de17f-126">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="de17f-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="de17f-127">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="de17f-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="de17f-128">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="de17f-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="de17f-129">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="de17f-130">Пример приложения регистрирует службу `IMyDependency` с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="de17f-131">Метод <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> регистрирует службу с заданной областью времени существования, временем существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="de17f-132">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="de17f-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="de17f-133">В примере приложения запрашивается служба `IMyDependency`, которая затем используется для вызова метода `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="de17f-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="de17f-134">Используя шаблон внедрения зависимостей, контроллер:</span><span class="sxs-lookup"><span data-stu-id="de17f-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="de17f-135">не использует конкретный тип `MyDependency`, только интерфейс `IMyDependency`, который он реализует.</span><span class="sxs-lookup"><span data-stu-id="de17f-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="de17f-136">Это упрощает изменение реализации, используемой контроллером, без изменения контроллера.</span><span class="sxs-lookup"><span data-stu-id="de17f-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="de17f-137">не создает экземпляр `MyDependency`, он создается контейнером внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="de17f-138">Реализацию интерфейса `IMyDependency` можно улучшить с помощью встроенного API ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="de17f-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="de17f-139">Обновленный метод `ConfigureServices` регистрирует новую реализацию `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="de17f-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="de17f-140">`MyDependency2` зависит от <xref:Microsoft.Extensions.Logging.ILogger%601>, который запрашивается в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="de17f-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="de17f-141">`ILogger<TCategoryName>` — это [предоставленная платформой служба](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="de17f-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="de17f-142">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="de17f-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="de17f-143">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="de17f-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="de17f-144">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="de17f-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="de17f-145">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="de17f-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="de17f-146">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="de17f-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="de17f-147">В терминологии внедрения зависимостей — служба:</span><span class="sxs-lookup"><span data-stu-id="de17f-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="de17f-148">Обычно является объектом, предоставляющим службу для других объектов, например службу `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="de17f-149">Не относится к веб-службе, хотя служба может использовать веб-службу.</span><span class="sxs-lookup"><span data-stu-id="de17f-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="de17f-150">Платформа предоставляет эффективную систему [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="de17f-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="de17f-151">Реализации `IMyDependency`, приведенные в предыдущем примере были написаны для демонстрации базового внедрения зависимостей, а не для реализации ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="de17f-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="de17f-152">Большинству приложений не нужно писать средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="de17f-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="de17f-153">В следующем коде показано использование журнала по умолчанию, для которого не требуется регистрация служб в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="de17f-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="de17f-154">Используя приведенный выше код, не нужно обновлять `ConfigureServices`, поскольку платформа предоставляет возможность [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="de17f-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="de17f-155">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="de17f-155">Services injected into Startup</span></span>

<span data-ttu-id="de17f-156">Службы можно внедрить в конструктор `Startup` и метод `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="de17f-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="de17f-157">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие службы:</span><span class="sxs-lookup"><span data-stu-id="de17f-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="de17f-158">Любая служба, зарегистрированная в контейнере внедрения зависимостей, может быть внедрена в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="de17f-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="de17f-159">Дополнительные сведения см. в разделах <xref:fundamentals/startup> и [Доступ к конфигурации во время запуска](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="de17f-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="de17f-160">Регистрация групп служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="de17f-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="de17f-161">Для регистрации группы связанных служб на платформе ASP.NET Core используется соглашение.</span><span class="sxs-lookup"><span data-stu-id="de17f-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="de17f-162">Соглашение заключается в использовании одного метода расширения `Add{GROUP_NAME}` для регистрации всех служб, необходимых компоненту платформы.</span><span class="sxs-lookup"><span data-stu-id="de17f-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="de17f-163">Например, метод расширения <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> регистрирует службы, необходимые для контроллеров MVC.</span><span class="sxs-lookup"><span data-stu-id="de17f-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="de17f-164">Следующий код создается шаблоном Razor Pages с использованием отдельных учетных записей пользователей. Он демонстрирует, как добавить дополнительные службы в контейнер с помощью методов расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="de17f-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="de17f-165">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="de17f-165">Service lifetimes</span></span>

<span data-ttu-id="de17f-166">Службы можно зарегистрировать с одним из следующих вариантов времени существования:</span><span class="sxs-lookup"><span data-stu-id="de17f-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="de17f-167">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-167">Transient</span></span>
* <span data-ttu-id="de17f-168">Область действия</span><span class="sxs-lookup"><span data-stu-id="de17f-168">Scoped</span></span>
* <span data-ttu-id="de17f-169">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-169">Singleton</span></span>

<span data-ttu-id="de17f-170">Они описываются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="de17f-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="de17f-171">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="de17f-172">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-172">Transient</span></span>

<span data-ttu-id="de17f-173">Временные службы времени существования создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="de17f-174">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="de17f-175">Регистрируйте временные службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span><span class="sxs-lookup"><span data-stu-id="de17f-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="de17f-176">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="de17f-177">Область действия</span><span class="sxs-lookup"><span data-stu-id="de17f-177">Scoped</span></span>

<span data-ttu-id="de17f-178">Службы времени существования с заданной областью создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="de17f-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="de17f-179">Регистрируйте службы с заданной областью с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span><span class="sxs-lookup"><span data-stu-id="de17f-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="de17f-180">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="de17f-181">При использовании Entity Framework Core метод расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> по умолчанию регистрирует типы `DbContext` с заданной областью времени существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="de17f-182">Разрешать службу с заданной областью из одноэлементного объекта ***запрещено***.</span><span class="sxs-lookup"><span data-stu-id="de17f-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="de17f-183">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="de17f-184">Допускается следующее:</span><span class="sxs-lookup"><span data-stu-id="de17f-184">It's fine to:</span></span>

* <span data-ttu-id="de17f-185">Разрешение одноэлементной службы из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="de17f-186">Разрешение службы с заданной областью из другой службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="de17f-187">По умолчанию в среде разработки разрешение службы из другой службы с более длинным временем существования вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="de17f-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="de17f-188">Дополнительные сведения см. в разделе [Проверка области](#sv).</span><span class="sxs-lookup"><span data-stu-id="de17f-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="de17f-189">Используйте службы с заданной областью в ПО промежуточного слоя, применяя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="de17f-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="de17f-190">Внедрите службу в метод `Invoke` или `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="de17f-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="de17f-191">С помощью [внедрите конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) создается исключение времени выполнения, поскольку оно заставляет службу с заданной областью вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="de17f-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="de17f-192">В примере в разделе [Параметры времени существования и регистрации](#lifetime-and-registration-options) демонстрируется подход `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="de17f-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="de17f-193">Используйте [фабричное ПО промежуточного слоя](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="de17f-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="de17f-194">ПО промежуточного слоя, зарегистрированное с использованием этого подхода, активируется при каждом клиентском запросе (подключении), что позволяет внедрять службы с заданной областью в метод `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="de17f-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="de17f-195">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="de17f-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="de17f-196">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-196">Singleton</span></span>

<span data-ttu-id="de17f-197">Одноэлементные службы времени существования создаются в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="de17f-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="de17f-198">При первом запросе.</span><span class="sxs-lookup"><span data-stu-id="de17f-198">The first time they're requested.</span></span>
* <span data-ttu-id="de17f-199">Разработчиком при предоставлении экземпляра реализации непосредственно в контейнер.</span><span class="sxs-lookup"><span data-stu-id="de17f-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="de17f-200">Этот подход требуется достаточно редко.</span><span class="sxs-lookup"><span data-stu-id="de17f-200">This approach is rarely needed.</span></span>

<span data-ttu-id="de17f-201">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="de17f-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="de17f-202">Если в приложении нужно использовать одноэлементные службы, разрешите контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="de17f-203">Не реализуйте одноэлементный подход и предоставьте код для удаления одноэлементных объектов.</span><span class="sxs-lookup"><span data-stu-id="de17f-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="de17f-204">Службы никогда не должны удаляться кодом, который разрешил службу из контейнера.</span><span class="sxs-lookup"><span data-stu-id="de17f-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="de17f-205">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="de17f-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="de17f-206">Зарегистрируйте одноэлементные службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="de17f-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="de17f-207">Одноэлементные службы должны быть потокобезопасными и часто использоваться в службах без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="de17f-208">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="de17f-209">Поскольку память не освобождается до завершения работы приложения, рекомендуется учитывать использование памяти одноэлементным объектом.</span><span class="sxs-lookup"><span data-stu-id="de17f-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="de17f-210">Разрешать службу с заданной областью из одноэлементного объекта ***запрещено***.</span><span class="sxs-lookup"><span data-stu-id="de17f-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="de17f-211">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="de17f-212">Допускается разрешать одноэлементную службу из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="de17f-213">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="de17f-213">Service registration methods</span></span>

<span data-ttu-id="de17f-214">Платформа предоставляет методы расширения регистрации службы, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="de17f-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="de17f-215">Метод</span><span class="sxs-lookup"><span data-stu-id="de17f-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="de17f-216">Автоматически</span><span class="sxs-lookup"><span data-stu-id="de17f-216">Automatic</span></span><br><span data-ttu-id="de17f-217">объект</span><span class="sxs-lookup"><span data-stu-id="de17f-217">object</span></span><br><span data-ttu-id="de17f-218">удаление</span><span class="sxs-lookup"><span data-stu-id="de17f-218">disposal</span></span> | <span data-ttu-id="de17f-219">Несколько</span><span class="sxs-lookup"><span data-stu-id="de17f-219">Multiple</span></span><br><span data-ttu-id="de17f-220">реализации</span><span class="sxs-lookup"><span data-stu-id="de17f-220">implementations</span></span> | <span data-ttu-id="de17f-221">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="de17f-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="de17f-222">Пример.</span><span class="sxs-lookup"><span data-stu-id="de17f-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="de17f-223">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-223">Yes</span></span>                             | <span data-ttu-id="de17f-224">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-224">Yes</span></span>                         | <span data-ttu-id="de17f-225">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-226">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="de17f-227">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-227">Yes</span></span>                             | <span data-ttu-id="de17f-228">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-228">Yes</span></span>                         | <span data-ttu-id="de17f-229">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="de17f-230">Пример.</span><span class="sxs-lookup"><span data-stu-id="de17f-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="de17f-231">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-231">Yes</span></span>                             | <span data-ttu-id="de17f-232">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-232">No</span></span>                          | <span data-ttu-id="de17f-233">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-234">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="de17f-235">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-235">No</span></span>                              | <span data-ttu-id="de17f-236">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-236">Yes</span></span>                         | <span data-ttu-id="de17f-237">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-238">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="de17f-239">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-239">No</span></span>                              | <span data-ttu-id="de17f-240">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-240">No</span></span>                          | <span data-ttu-id="de17f-241">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-241">Yes</span></span>       |

<span data-ttu-id="de17f-242">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="de17f-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="de17f-243">Распространенный сценарий для использования нескольких реализаций — [макетирование типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="de17f-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="de17f-244">Платформа также предоставляет методы расширения `TryAdd{LIFETIME}`, которые регистрируют службу только в том случае, если реализация еще не зарегистрирована.</span><span class="sxs-lookup"><span data-stu-id="de17f-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="de17f-245">В следующем примере вызов `AddSingleton` регистрирует `MyDependency` как реализацию для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="de17f-246">Вызов `TryAddSingleton` ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="de17f-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="de17f-247">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="de17f-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="de17f-248">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="de17f-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="de17f-249">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="de17f-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="de17f-250">При регистрации служб разработчику необходимо добавить экземпляр в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="de17f-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="de17f-251">Как правило, авторы библиотек используют `TryAddEnumerable`, чтобы избежать регистрации нескольких копий реализации в контейнере.</span><span class="sxs-lookup"><span data-stu-id="de17f-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="de17f-252">В следующем примере первый вызов `TryAddEnumerable` регистрирует `MyDependency` как реализацию для `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="de17f-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="de17f-253">Второй вызов регистрирует `MyDependency` для `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="de17f-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="de17f-254">Третий вызов ничего не делает, поскольку у `IMyDependency1` уже есть зарегистрированная реализация `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="de17f-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="de17f-255">Регистрация службы обычно не зависит от порядка, за исключением случаев регистрации нескольких реализаций одного типа.</span><span class="sxs-lookup"><span data-stu-id="de17f-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="de17f-256">`IServiceCollection` является коллекцией объектов <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="de17f-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="de17f-257">В следующем примере показано, как зарегистрировать службу, создав и добавив `ServiceDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="de17f-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="de17f-258">Встроенные методы `Add{LIFETIME}` используют аналогичный подход.</span><span class="sxs-lookup"><span data-stu-id="de17f-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="de17f-259">Например, см. [исходный код для AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="de17f-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="de17f-260">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="de17f-260">Constructor injection behavior</span></span>

<span data-ttu-id="de17f-261">Службы можно разрешать с помощью:</span><span class="sxs-lookup"><span data-stu-id="de17f-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="de17f-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="de17f-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="de17f-263">Создает объекты, которые не зарегистрированы в контейнере.</span><span class="sxs-lookup"><span data-stu-id="de17f-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="de17f-264">Используется с функциями платформы, такими как [вспомогательные приложения для тегов](xref:mvc/views/tag-helpers/intro), контроллеры MVC и [средства привязки моделей](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="de17f-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="de17f-265">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="de17f-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="de17f-266">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="de17f-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="de17f-267">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="de17f-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="de17f-268">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="de17f-269">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="de17f-269">Entity Framework contexts</span></span>

<span data-ttu-id="de17f-270">По умолчанию контексты Entity Framework добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="de17f-271">Чтобы использовать другое время существования, укажите его с помощью перегрузки <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="de17f-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="de17f-272">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="de17f-273">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="de17f-273">Lifetime and registration options</span></span>

<span data-ttu-id="de17f-274">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации службы, рассмотрим интерфейсы, представляющие задачу в виде операции с идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="de17f-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="de17f-275">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="de17f-276">Следующий класс `Operation` реализует все предыдущие интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="de17f-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="de17f-277">Конструктор `Operation` создает идентификатор GUID и сохраняет последние 4 символа в свойстве `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="de17f-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="de17f-278">Метод `Startup.ConfigureServices` создает несколько регистраций класса `Operation` в соответствии с именованным временем существования:</span><span class="sxs-lookup"><span data-stu-id="de17f-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="de17f-279">В примере приложения показано время существования объектов в пределах запросов и между запросами.</span><span class="sxs-lookup"><span data-stu-id="de17f-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="de17f-280">`IndexModel` и ПО промежуточного слоя запрашивают каждый тип `IOperation` и регистрируют `OperationId` для каждого из них:</span><span class="sxs-lookup"><span data-stu-id="de17f-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="de17f-281">Аналогично `IndexModel`, ПО промежуточного слоя и разрешает те же службы:</span><span class="sxs-lookup"><span data-stu-id="de17f-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="de17f-282">Службы с заданной областью должны быть разрешены в методе `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="de17f-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="de17f-283">Выходные данные средства ведения журнала содержат:</span><span class="sxs-lookup"><span data-stu-id="de17f-283">The logger output shows:</span></span>

* <span data-ttu-id="de17f-284">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="de17f-284">*Transient* objects are always different.</span></span> <span data-ttu-id="de17f-285">Значение временного `OperationId` отличается в `IndexModel` и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="de17f-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="de17f-286">Объекты с *заданной областью* остаются неизменными в пределах одного запроса, но в разных запросах используются разные объекты.</span><span class="sxs-lookup"><span data-stu-id="de17f-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="de17f-287">*Одноэлементные* объекты одинаковы для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="de17f-288">Чтобы уменьшить объем выводимых данных журнала, задайте в файле *appsettings.Development.json* параметр "Logging:LogLevel:Microsoft:Error".</span><span class="sxs-lookup"><span data-stu-id="de17f-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="de17f-289">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="de17f-289">Call services from main</span></span>

<span data-ttu-id="de17f-290">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="de17f-291">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="de17f-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="de17f-292">В следующем примере показано, как получить доступ к службе `IMyDependency` с заданной областью и вызвать ее метод `WriteMessage` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="de17f-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="de17f-293">Проверка области</span><span class="sxs-lookup"><span data-stu-id="de17f-293">Scope validation</span></span>

<span data-ttu-id="de17f-294">Когда приложение выполняется в [среде разработки](xref:fundamentals/environments) и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="de17f-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="de17f-295">Службы с заданной областью не разрешаются из корневого поставщика службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="de17f-296">Службы с заданной областью не вводятся в одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="de17f-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="de17f-297">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="de17f-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="de17f-298">Время существования корневого поставщика службы соответствует времени существования приложения — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="de17f-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="de17f-299">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="de17f-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="de17f-300">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="de17f-301">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="de17f-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="de17f-302">Дополнительные сведения см. в разделе [Проверка области](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="de17f-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="de17f-303">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="de17f-303">Request Services</span></span>

<span data-ttu-id="de17f-304">Службы, доступные в пределах запроса ASP.NET Core, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="de17f-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="de17f-305">При запросе в рамках запроса службы и их зависимости разрешаются из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="de17f-306">Платформа создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="de17f-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="de17f-307">Все службы с заданной областью действительны до тех пор, пока запрос активен.</span><span class="sxs-lookup"><span data-stu-id="de17f-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="de17f-308">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не разрешать службы из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="de17f-309">В результате создаются классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="de17f-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="de17f-310">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-310">Design services for dependency injection</span></span>

<span data-ttu-id="de17f-311">При разработке служб для внедрения зависимостей придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="de17f-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="de17f-312">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="de17f-313">Избегайте создания глобального состояния. Для этого проектируйте приложения для использования отдельных служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="de17f-314">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="de17f-315">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="de17f-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="de17f-316">Сделайте службы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="de17f-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="de17f-317">Если класс имеет слишком много внедренных зависимостей, это может указывать на то, что у класса слишком много задач и он нарушает [принцип единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="de17f-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="de17f-318">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новые классы.</span><span class="sxs-lookup"><span data-stu-id="de17f-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="de17f-319">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="de17f-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="de17f-320">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="de17f-320">Disposal of services</span></span>

<span data-ttu-id="de17f-321">Контейнер вызывает <xref:System.IDisposable.Dispose%2A> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="de17f-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="de17f-322">Службы, разрешенные из контейнера, никогда не должны удаляться разработчиком.</span><span class="sxs-lookup"><span data-stu-id="de17f-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="de17f-323">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="de17f-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="de17f-324">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="de17f-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="de17f-325">После каждого обновления страницы индекса в консоли отладки отображаются следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="de17f-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="de17f-326">Службы, не созданные контейнером службы</span><span class="sxs-lookup"><span data-stu-id="de17f-326">Services not created by the service container</span></span>

<span data-ttu-id="de17f-327">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="de17f-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="de17f-328">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="de17f-328">In the preceding code:</span></span>

* <span data-ttu-id="de17f-329">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="de17f-330">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="de17f-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="de17f-331">За удаление служб отвечает разработчик.</span><span class="sxs-lookup"><span data-stu-id="de17f-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="de17f-332">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="de17f-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="de17f-333">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-333">Transient, limited lifetime</span></span>

<span data-ttu-id="de17f-334">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="de17f-334">**Scenario**</span></span>

<span data-ttu-id="de17f-335">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="de17f-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="de17f-336">Экземпляр разрешается в корневой области (в корневом контейнере).</span><span class="sxs-lookup"><span data-stu-id="de17f-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="de17f-337">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="de17f-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="de17f-338">**Решение**</span><span class="sxs-lookup"><span data-stu-id="de17f-338">**Solution**</span></span>

<span data-ttu-id="de17f-339">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="de17f-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="de17f-340">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="de17f-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="de17f-341">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="de17f-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="de17f-342">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="de17f-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="de17f-343">Используйте <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="de17f-344">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="de17f-345">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="de17f-345">**Scenario**</span></span>

<span data-ttu-id="de17f-346">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для экземпляра <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="de17f-347">**Решение**</span><span class="sxs-lookup"><span data-stu-id="de17f-347">**Solution**</span></span>

<span data-ttu-id="de17f-348">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="de17f-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="de17f-349">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для создания нового <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="de17f-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="de17f-350">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="de17f-351">Удалите область, если она больше не нужна.</span><span class="sxs-lookup"><span data-stu-id="de17f-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="de17f-352">Общие рекомендации по IDisposable</span><span class="sxs-lookup"><span data-stu-id="de17f-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="de17f-353">Не регистрируйте экземпляры <xref:System.IDisposable> с временным временем существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="de17f-354">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="de17f-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="de17f-355">Не разрешайте экземпляры <xref:System.IDisposable> с временным временем существования или временем существования с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="de17f-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="de17f-356">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, но это не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="de17f-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="de17f-357">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="de17f-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="de17f-358">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="de17f-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="de17f-359">Области должны использоваться для управления временем существования служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="de17f-360">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="de17f-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="de17f-361">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="de17f-361">Default service container replacement</span></span>

<span data-ttu-id="de17f-362">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="de17f-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="de17f-363">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="de17f-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="de17f-364">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="de17f-364">Property injection</span></span>
* <span data-ttu-id="de17f-365">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="de17f-365">Injection based on name</span></span>
* <span data-ttu-id="de17f-366">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="de17f-366">Child containers</span></span>
* <span data-ttu-id="de17f-367">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="de17f-367">Custom lifetime management</span></span>
* <span data-ttu-id="de17f-368">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="de17f-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="de17f-369">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="de17f-369">Convention-based registration</span></span>

<span data-ttu-id="de17f-370">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="de17f-371">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="de17f-371">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="de17f-372">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-372">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="de17f-373">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="de17f-373">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="de17f-374">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-374">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="de17f-375">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="de17f-375">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="de17f-376">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-376">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="de17f-377">Unity</span><span class="sxs-lookup"><span data-stu-id="de17f-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="de17f-378">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="de17f-378">Thread safety</span></span>

<span data-ttu-id="de17f-379">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="de17f-380">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="de17f-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="de17f-381">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="de17f-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="de17f-382">Как и конструктор типов (`static`), он гарантированно будет вызываться только один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="de17f-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="de17f-383">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="de17f-383">Recommendations</span></span>

* <span data-ttu-id="de17f-384">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de17f-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="de17f-385">Так как C# не поддерживает асинхронные конструкторы, следует использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="de17f-386">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="de17f-387">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="de17f-388">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="de17f-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="de17f-389">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к другому объекту.</span><span class="sxs-lookup"><span data-stu-id="de17f-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="de17f-390">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="de17f-391">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="de17f-391">Avoid static access to services.</span></span> <span data-ttu-id="de17f-392">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) в качестве статического поля или свойства.</span><span class="sxs-lookup"><span data-stu-id="de17f-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="de17f-393">Обеспечьте быстродействие и синхронизацию фабрик DI.</span><span class="sxs-lookup"><span data-stu-id="de17f-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="de17f-394">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="de17f-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="de17f-395">Например, не вызывайте <xref:System.IServiceProvider.GetService%2A> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="de17f-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="de17f-396">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="de17f-396">**Incorrect:**</span></span>

    ![Неверный код](dependency-injection/_static/bad.png)

  <span data-ttu-id="de17f-398">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="de17f-398">**Correct**:</span></span>

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
* <span data-ttu-id="de17f-399">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="de17f-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="de17f-400">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="de17f-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="de17f-401">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="de17f-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="de17f-402">Избегайте вызовов <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="de17f-403">Вызов `BuildServiceProvider` обычно происходит, когда разработчику необходимо разрешить службу в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="de17f-404">Например, рассмотрим случай, когда `LoginPath` загружается из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="de17f-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="de17f-405">Добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="de17f-405">Avoid the following approach:</span></span>

  ![Неверный код при вызове BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="de17f-407">На предыдущем рисунке при выборе строки, отмеченной зеленой волнистой линией в разделе `services.BuildServiceProvider`, отображается следующее предупреждение ASP0000:</span><span class="sxs-lookup"><span data-stu-id="de17f-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="de17f-408">ASP0000. Вызов BuildServiceProvider из кода приложения приводит к созданию дополнительной копии создаваемых одноэлементных служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="de17f-409">В качестве параметров для Configure можно использовать альтернативные варианты, такие как службы внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="de17f-410">При вызове `BuildServiceProvider` создается второй контейнер, который может создавать разорванные одноэлементные экземпляры и ссылаться на графы объектов в нескольких контейнерах.</span><span class="sxs-lookup"><span data-stu-id="de17f-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="de17f-411">Правильный способ получения `LoginPath` — использование встроенной поддержки шаблона параметров для внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="de17f-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="de17f-412">Неудаляемые временные службы фиксируются контейнером для их удаления.</span><span class="sxs-lookup"><span data-stu-id="de17f-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="de17f-413">Это может привести к утечке памяти, если она разрешена из контейнера верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="de17f-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="de17f-414">Включите проверку области, чтобы убедиться, что в приложении нет отдельных объектов, записывающих службы с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="de17f-414">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="de17f-415">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="de17f-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="de17f-416">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="de17f-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="de17f-417">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="de17f-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="de17f-418">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="de17f-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="de17f-419">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="de17f-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="de17f-420">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="de17f-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) — это платформа приложений для создания модульных мультитенантных приложений в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de17f-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="de17f-422">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="de17f-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="de17f-423">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. [здесь](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="de17f-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="de17f-424">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="de17f-424">Framework-provided services</span></span>

<span data-ttu-id="de17f-425">Метод `Startup.ConfigureServices` регистрирует службы, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="de17f-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="de17f-426">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="de17f-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="de17f-427">Для приложений, основанных на шаблонах ASP.NET Core, платформа регистрирует более 250 служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="de17f-428">В следующей таблице перечислены некоторые примеры этих зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="de17f-429">Тип службы</span><span class="sxs-lookup"><span data-stu-id="de17f-429">Service Type</span></span>                                                                                    | <span data-ttu-id="de17f-430">Время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="de17f-431">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="de17f-432">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="de17f-433">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="de17f-434">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="de17f-435">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="de17f-436">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="de17f-437">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="de17f-438">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="de17f-439">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="de17f-440">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="de17f-441">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="de17f-442">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="de17f-443">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="de17f-444">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="de17f-445">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="de17f-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="de17f-446">Шаблоны конференций NDC для разработки приложений с внедрением зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="de17f-447">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de17f-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="de17f-448">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="de17f-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="de17f-449">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="de17f-450">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="de17f-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="de17f-451">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="de17f-451">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="de17f-452">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="de17f-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="de17f-453">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="de17f-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="de17f-454">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="de17f-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="de17f-455">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de17f-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="de17f-456">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-456">Overview of dependency injection</span></span>

<span data-ttu-id="de17f-457">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="de17f-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="de17f-458">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="de17f-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="de17f-459">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="de17f-460">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="de17f-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="de17f-461">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="de17f-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="de17f-462">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="de17f-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="de17f-463">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="de17f-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="de17f-464">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="de17f-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="de17f-465">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="de17f-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="de17f-466">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="de17f-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="de17f-467">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="de17f-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="de17f-468">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="de17f-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="de17f-469">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="de17f-470">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="de17f-471">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="de17f-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="de17f-472">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="de17f-473">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="de17f-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="de17f-474">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="de17f-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="de17f-475">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="de17f-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="de17f-476">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="de17f-477">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="de17f-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="de17f-478">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="de17f-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="de17f-479">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="de17f-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="de17f-480">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="de17f-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="de17f-481">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="de17f-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="de17f-482">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="de17f-483">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="de17f-484">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="de17f-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="de17f-485">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="de17f-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="de17f-486">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="de17f-487">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="de17f-488">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="de17f-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="de17f-489">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="de17f-490">Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="de17f-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="de17f-491">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="de17f-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="de17f-492">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="de17f-493">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="de17f-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="de17f-494">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="de17f-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="de17f-495">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="de17f-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="de17f-496">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="de17f-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="de17f-497">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="de17f-497">Services injected into Startup</span></span>

<span data-ttu-id="de17f-498">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="de17f-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="de17f-499">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="de17f-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="de17f-500">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="de17f-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="de17f-501">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="de17f-501">Framework-provided services</span></span>

<span data-ttu-id="de17f-502">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="de17f-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="de17f-503">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="de17f-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="de17f-504">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="de17f-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="de17f-505">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="de17f-506">Тип службы</span><span class="sxs-lookup"><span data-stu-id="de17f-506">Service Type</span></span> | <span data-ttu-id="de17f-507">Время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="de17f-508">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="de17f-509">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="de17f-510">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="de17f-511">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="de17f-512">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="de17f-513">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="de17f-514">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="de17f-515">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="de17f-516">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="de17f-517">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="de17f-518">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="de17f-519">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="de17f-520">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="de17f-521">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="de17f-522">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="de17f-522">Register additional services with extension methods</span></span>

<span data-ttu-id="de17f-523">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="de17f-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="de17f-524">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="de17f-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="de17f-525">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="de17f-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="de17f-526">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="de17f-526">Service lifetimes</span></span>

<span data-ttu-id="de17f-527">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="de17f-528">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="de17f-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="de17f-529">Временный</span><span class="sxs-lookup"><span data-stu-id="de17f-529">Transient</span></span>

<span data-ttu-id="de17f-530">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="de17f-531">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="de17f-532">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="de17f-533">Область действия</span><span class="sxs-lookup"><span data-stu-id="de17f-533">Scoped</span></span>

<span data-ttu-id="de17f-534">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="de17f-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="de17f-535">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="de17f-536">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="de17f-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="de17f-537">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="de17f-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="de17f-538">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="de17f-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="de17f-539">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="de17f-539">Singleton</span></span>

<span data-ttu-id="de17f-540">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="de17f-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="de17f-541">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="de17f-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="de17f-542">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="de17f-543">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="de17f-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="de17f-544">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="de17f-545">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="de17f-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="de17f-546">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="de17f-547">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="de17f-547">Service registration methods</span></span>

<span data-ttu-id="de17f-548">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="de17f-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="de17f-549">Метод</span><span class="sxs-lookup"><span data-stu-id="de17f-549">Method</span></span> | <span data-ttu-id="de17f-550">Автоматически</span><span class="sxs-lookup"><span data-stu-id="de17f-550">Automatic</span></span><br><span data-ttu-id="de17f-551">объект</span><span class="sxs-lookup"><span data-stu-id="de17f-551">object</span></span><br><span data-ttu-id="de17f-552">удаление</span><span class="sxs-lookup"><span data-stu-id="de17f-552">disposal</span></span> | <span data-ttu-id="de17f-553">Несколько</span><span class="sxs-lookup"><span data-stu-id="de17f-553">Multiple</span></span><br><span data-ttu-id="de17f-554">реализации</span><span class="sxs-lookup"><span data-stu-id="de17f-554">implementations</span></span> | <span data-ttu-id="de17f-555">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="de17f-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="de17f-556">Пример.</span><span class="sxs-lookup"><span data-stu-id="de17f-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="de17f-557">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-557">Yes</span></span> | <span data-ttu-id="de17f-558">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-558">Yes</span></span> | <span data-ttu-id="de17f-559">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-560">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="de17f-561">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-561">Yes</span></span> | <span data-ttu-id="de17f-562">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-562">Yes</span></span> | <span data-ttu-id="de17f-563">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="de17f-564">Пример.</span><span class="sxs-lookup"><span data-stu-id="de17f-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="de17f-565">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-565">Yes</span></span> | <span data-ttu-id="de17f-566">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-566">No</span></span> | <span data-ttu-id="de17f-567">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-568">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="de17f-569">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-569">No</span></span> | <span data-ttu-id="de17f-570">да</span><span class="sxs-lookup"><span data-stu-id="de17f-570">Yes</span></span> | <span data-ttu-id="de17f-571">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="de17f-572">Примеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="de17f-573">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-573">No</span></span> | <span data-ttu-id="de17f-574">Нет</span><span class="sxs-lookup"><span data-stu-id="de17f-574">No</span></span> | <span data-ttu-id="de17f-575">Да</span><span class="sxs-lookup"><span data-stu-id="de17f-575">Yes</span></span> |

<span data-ttu-id="de17f-576">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="de17f-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="de17f-577">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="de17f-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="de17f-578">Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация.</span><span class="sxs-lookup"><span data-stu-id="de17f-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="de17f-579">В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="de17f-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="de17f-580">Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:</span><span class="sxs-lookup"><span data-stu-id="de17f-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="de17f-581">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="de17f-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="de17f-582">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="de17f-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="de17f-583">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="de17f-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="de17f-584">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="de17f-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="de17f-585">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="de17f-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="de17f-586">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="de17f-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="de17f-587">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="de17f-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="de17f-588">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="de17f-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="de17f-589">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="de17f-589">Constructor injection behavior</span></span>

<span data-ttu-id="de17f-590">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="de17f-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="de17f-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="de17f-592">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="de17f-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="de17f-593">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="de17f-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="de17f-594">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="de17f-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="de17f-595">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="de17f-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="de17f-596">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="de17f-597">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="de17f-597">Entity Framework contexts</span></span>

<span data-ttu-id="de17f-598">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="de17f-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="de17f-599">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="de17f-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="de17f-600">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="de17f-601">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="de17f-601">Lifetime and registration options</span></span>

<span data-ttu-id="de17f-602">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="de17f-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="de17f-603">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="de17f-604">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="de17f-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="de17f-605">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="de17f-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="de17f-606">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="de17f-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="de17f-607">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="de17f-608">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="de17f-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="de17f-609">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="de17f-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="de17f-610">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="de17f-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="de17f-611">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="de17f-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="de17f-612">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="de17f-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="de17f-613">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="de17f-614">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="de17f-615">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="de17f-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="de17f-616">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="de17f-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="de17f-617">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="de17f-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="de17f-618">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="de17f-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="de17f-619">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="de17f-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="de17f-620">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="de17f-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="de17f-621">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="de17f-621">**First request:**</span></span>

<span data-ttu-id="de17f-622">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="de17f-622">Controller operations:</span></span>

<span data-ttu-id="de17f-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="de17f-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="de17f-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="de17f-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="de17f-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="de17f-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="de17f-626">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="de17f-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="de17f-627">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="de17f-627">`OperationService` operations:</span></span>

<span data-ttu-id="de17f-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="de17f-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="de17f-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="de17f-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="de17f-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="de17f-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="de17f-631">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="de17f-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="de17f-632">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="de17f-632">**Second request:**</span></span>

<span data-ttu-id="de17f-633">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="de17f-633">Controller operations:</span></span>

<span data-ttu-id="de17f-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="de17f-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="de17f-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="de17f-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="de17f-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="de17f-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="de17f-637">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="de17f-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="de17f-638">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="de17f-638">`OperationService` operations:</span></span>

<span data-ttu-id="de17f-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="de17f-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="de17f-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="de17f-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="de17f-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="de17f-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="de17f-642">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="de17f-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="de17f-643">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="de17f-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="de17f-644">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="de17f-644">*Transient* objects are always different.</span></span> <span data-ttu-id="de17f-645">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="de17f-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="de17f-646">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="de17f-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="de17f-647">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="de17f-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="de17f-648">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="de17f-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="de17f-649">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="de17f-649">Call services from main</span></span>

<span data-ttu-id="de17f-650">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="de17f-651">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="de17f-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="de17f-652">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="de17f-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="de17f-653">Проверка области</span><span class="sxs-lookup"><span data-stu-id="de17f-653">Scope validation</span></span>

<span data-ttu-id="de17f-654">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="de17f-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="de17f-655">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="de17f-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="de17f-656">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="de17f-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="de17f-657">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="de17f-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="de17f-658">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="de17f-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="de17f-659">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="de17f-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="de17f-660">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="de17f-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="de17f-661">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="de17f-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="de17f-662">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="de17f-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="de17f-663">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="de17f-663">Request Services</span></span>

<span data-ttu-id="de17f-664">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="de17f-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="de17f-665">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="de17f-666">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="de17f-667">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="de17f-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="de17f-668">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="de17f-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="de17f-669">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="de17f-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="de17f-670">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="de17f-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="de17f-671">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-671">Design services for dependency injection</span></span>

<span data-ttu-id="de17f-672">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="de17f-672">Best practices are to:</span></span>

* <span data-ttu-id="de17f-673">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="de17f-674">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="de17f-675">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="de17f-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="de17f-676">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="de17f-677">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="de17f-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="de17f-678">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="de17f-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="de17f-679">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="de17f-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="de17f-680">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="de17f-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="de17f-681">Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="de17f-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="de17f-682">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="de17f-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="de17f-683">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="de17f-683">Disposal of services</span></span>

<span data-ttu-id="de17f-684">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="de17f-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="de17f-685">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="de17f-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="de17f-686">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="de17f-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="de17f-687">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="de17f-687">In the following example:</span></span>

* <span data-ttu-id="de17f-688">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="de17f-689">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="de17f-690">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="de17f-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="de17f-691">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="de17f-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="de17f-692">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="de17f-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="de17f-693">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-693">Transient, limited lifetime</span></span>

<span data-ttu-id="de17f-694">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="de17f-694">**Scenario**</span></span>

<span data-ttu-id="de17f-695">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="de17f-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="de17f-696">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="de17f-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="de17f-697">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="de17f-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="de17f-698">**Решение**</span><span class="sxs-lookup"><span data-stu-id="de17f-698">**Solution**</span></span>

<span data-ttu-id="de17f-699">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="de17f-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="de17f-700">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="de17f-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="de17f-701">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="de17f-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="de17f-702">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="de17f-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="de17f-703">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="de17f-704">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="de17f-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="de17f-705">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="de17f-705">**Scenario**</span></span>

<span data-ttu-id="de17f-706">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="de17f-707">**Решение**</span><span class="sxs-lookup"><span data-stu-id="de17f-707">**Solution**</span></span>

<span data-ttu-id="de17f-708">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="de17f-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="de17f-709">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="de17f-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="de17f-710">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="de17f-711">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="de17f-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="de17f-712">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="de17f-712">General Guidelines</span></span>

* <span data-ttu-id="de17f-713">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="de17f-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="de17f-714">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="de17f-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="de17f-715">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="de17f-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="de17f-716">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="de17f-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="de17f-717">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="de17f-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="de17f-718">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="de17f-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="de17f-719">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="de17f-720">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="de17f-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="de17f-721">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="de17f-721">Default service container replacement</span></span>

<span data-ttu-id="de17f-722">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="de17f-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="de17f-723">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="de17f-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="de17f-724">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="de17f-724">Property injection</span></span>
* <span data-ttu-id="de17f-725">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="de17f-725">Injection based on name</span></span>
* <span data-ttu-id="de17f-726">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="de17f-726">Child containers</span></span>
* <span data-ttu-id="de17f-727">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="de17f-727">Custom lifetime management</span></span>
* <span data-ttu-id="de17f-728">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="de17f-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="de17f-729">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="de17f-729">Convention-based registration</span></span>

<span data-ttu-id="de17f-730">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="de17f-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="de17f-731">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="de17f-731">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="de17f-732">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-732">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="de17f-733">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="de17f-733">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="de17f-734">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-734">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="de17f-735">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="de17f-735">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="de17f-736">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="de17f-736">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="de17f-737">Unity</span><span class="sxs-lookup"><span data-stu-id="de17f-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="de17f-738">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="de17f-738">Thread safety</span></span>

<span data-ttu-id="de17f-739">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="de17f-740">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="de17f-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="de17f-741">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="de17f-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="de17f-742">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="de17f-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="de17f-743">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="de17f-743">Recommendations</span></span>

* <span data-ttu-id="de17f-744">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de17f-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="de17f-745">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="de17f-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="de17f-746">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="de17f-747">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="de17f-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="de17f-748">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="de17f-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="de17f-749">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="de17f-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="de17f-750">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="de17f-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="de17f-751">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="de17f-751">Avoid static access to services.</span></span> <span data-ttu-id="de17f-752">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).</span><span class="sxs-lookup"><span data-stu-id="de17f-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="de17f-753">Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="de17f-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="de17f-754">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="de17f-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="de17f-755">**Неправильно**:</span><span class="sxs-lookup"><span data-stu-id="de17f-755">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="de17f-756">**Правильно**:</span><span class="sxs-lookup"><span data-stu-id="de17f-756">**Correct**:</span></span>

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

* <span data-ttu-id="de17f-757">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="de17f-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="de17f-758">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="de17f-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="de17f-759">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="de17f-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="de17f-760">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="de17f-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="de17f-761">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="de17f-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="de17f-762">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="de17f-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de17f-763">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="de17f-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="de17f-764">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de17f-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="de17f-765">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="de17f-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="de17f-766">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="de17f-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="de17f-767">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="de17f-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="de17f-768">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="de17f-768">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
