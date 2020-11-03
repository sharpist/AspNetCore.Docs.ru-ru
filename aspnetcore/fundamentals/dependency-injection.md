---
title: Внедрение зависимостей в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core реализует внедрение зависимостей и как его использовать.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678570"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d4c88-103">Внедрение зависимостей в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4c88-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4c88-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="d4c88-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d4c88-105">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="d4c88-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d4c88-106">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d4c88-107">Дополнительные сведения об использовании внедрения зависимостей в консольных приложениях см. в разделе [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d4c88-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="d4c88-108">Дополнительные сведения о внедрении параметров зависимостей см. в разделе <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d4c88-109">В этой статье приводятся сведения о внедрении зависимостей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4c88-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="d4c88-110">Дополнительные сведения об использовании внедрения зависимостей в консольных приложениях см. в разделе [Внедрение зависимостей в .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d4c88-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="d4c88-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4c88-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d4c88-112">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-112">Overview of dependency injection</span></span>

<span data-ttu-id="d4c88-113">*Зависимость*  — это любой объект, от которого зависит другой объект.</span><span class="sxs-lookup"><span data-stu-id="d4c88-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="d4c88-114">Рассмотрим следующий класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы:</span><span class="sxs-lookup"><span data-stu-id="d4c88-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="d4c88-115">Класс может создать экземпляр класса `MyDependency`, чтобы использовать его метод `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="d4c88-116">В следующем примере класс `MyDependency` выступает зависимостью класса `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d4c88-117">Этот класс создает `MyDependency` и напрямую зависит от этого класса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="d4c88-118">Зависимости в коде, как в предыдущем примере, представляют собой определенную проблему. Их следует избегать по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d4c88-119">Чтобы заменить `MyDependency` другой реализацией, класс `IndexModel` необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="d4c88-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="d4c88-120">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="d4c88-121">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="d4c88-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d4c88-122">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d4c88-123">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="d4c88-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d4c88-124">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="d4c88-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d4c88-125">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d4c88-126">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d4c88-127">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d4c88-128">Как правило, службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d4c88-129">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="d4c88-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d4c88-130">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="d4c88-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d4c88-131">В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d4c88-132">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d4c88-133">Пример приложения регистрирует службу `IMyDependency` с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d4c88-134">Метод <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> регистрирует службу с заданной областью времени существования, временем существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="d4c88-135">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="d4c88-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="d4c88-136">В примере приложения запрашивается служба `IMyDependency`, которая затем используется для вызова метода `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="d4c88-137">Используя шаблон внедрения зависимостей, контроллер:</span><span class="sxs-lookup"><span data-stu-id="d4c88-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="d4c88-138">не использует конкретный тип `MyDependency`, только интерфейс `IMyDependency`, который он реализует.</span><span class="sxs-lookup"><span data-stu-id="d4c88-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="d4c88-139">Это упрощает изменение реализации, используемой контроллером, без изменения контроллера.</span><span class="sxs-lookup"><span data-stu-id="d4c88-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="d4c88-140">не создает экземпляр `MyDependency`, он создается контейнером внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="d4c88-141">Реализацию интерфейса `IMyDependency` можно улучшить с помощью встроенного API ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="d4c88-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="d4c88-142">Обновленный метод `ConfigureServices` регистрирует новую реализацию `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="d4c88-143">`MyDependency2` зависит от <xref:Microsoft.Extensions.Logging.ILogger%601>, который запрашивается в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="d4c88-144">`ILogger<TCategoryName>` — это [предоставленная платформой служба](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="d4c88-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="d4c88-145">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="d4c88-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d4c88-146">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="d4c88-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d4c88-147">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d4c88-148">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей* , *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="d4c88-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="d4c88-149">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="d4c88-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="d4c88-150">В терминологии внедрения зависимостей — служба:</span><span class="sxs-lookup"><span data-stu-id="d4c88-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="d4c88-151">Обычно является объектом, предоставляющим службу для других объектов, например службу `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="d4c88-152">Не относится к веб-службе, хотя служба может использовать веб-службу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="d4c88-153">Платформа предоставляет эффективную систему [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d4c88-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="d4c88-154">Реализации `IMyDependency`, приведенные в предыдущем примере были написаны для демонстрации базового внедрения зависимостей, а не для реализации ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d4c88-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="d4c88-155">Большинству приложений не нужно писать средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d4c88-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="d4c88-156">В следующем коде показано использование журнала по умолчанию, для которого не требуется регистрация служб в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d4c88-157">Используя приведенный выше код, не нужно обновлять `ConfigureServices`, поскольку платформа предоставляет возможность [ведения журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d4c88-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="d4c88-158">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="d4c88-158">Services injected into Startup</span></span>

<span data-ttu-id="d4c88-159">Службы можно внедрить в конструктор `Startup` и метод `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="d4c88-160">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие службы:</span><span class="sxs-lookup"><span data-stu-id="d4c88-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d4c88-161">Любая служба, зарегистрированная в контейнере внедрения зависимостей, может быть внедрена в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="d4c88-162">Дополнительные сведения см. в разделах <xref:fundamentals/startup> и [Доступ к конфигурации во время запуска](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d4c88-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="d4c88-163">Регистрация групп служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="d4c88-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="d4c88-164">Для регистрации группы связанных служб на платформе ASP.NET Core используется соглашение.</span><span class="sxs-lookup"><span data-stu-id="d4c88-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="d4c88-165">Соглашение заключается в использовании одного метода расширения `Add{GROUP_NAME}` для регистрации всех служб, необходимых компоненту платформы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="d4c88-166">Например, метод расширения <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> регистрирует службы, необходимые для контроллеров MVC.</span><span class="sxs-lookup"><span data-stu-id="d4c88-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="d4c88-167">Следующий код создается шаблоном :::no-loc(Razor)::: Pages с использованием отдельных учетных записей пользователей. Он демонстрирует, как добавить дополнительные службы в контейнер с помощью методов расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> и <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span><span class="sxs-lookup"><span data-stu-id="d4c88-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="d4c88-168">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-168">Service lifetimes</span></span>

<span data-ttu-id="d4c88-169">Службы можно зарегистрировать с одним из следующих вариантов времени существования:</span><span class="sxs-lookup"><span data-stu-id="d4c88-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="d4c88-170">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-170">Transient</span></span>
* <span data-ttu-id="d4c88-171">Область действия</span><span class="sxs-lookup"><span data-stu-id="d4c88-171">Scoped</span></span>
* <span data-ttu-id="d4c88-172">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-172">Singleton</span></span>

<span data-ttu-id="d4c88-173">Они описываются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="d4c88-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="d4c88-174">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="d4c88-175">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-175">Transient</span></span>

<span data-ttu-id="d4c88-176">Временные службы времени существования создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="d4c88-177">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="d4c88-178">Регистрируйте временные службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="d4c88-179">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d4c88-180">Область действия</span><span class="sxs-lookup"><span data-stu-id="d4c88-180">Scoped</span></span>

<span data-ttu-id="d4c88-181">Службы времени существования с заданной областью создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="d4c88-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="d4c88-182">Регистрируйте службы с заданной областью с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="d4c88-183">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="d4c88-184">При использовании Entity Framework Core метод расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> по умолчанию регистрирует типы `DbContext` с заданной областью времени существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="d4c88-185">Разрешать службу с заданной областью из одноэлементного объекта \* **запрещено** _, и будьте внимательны, чтобы не сделать это неявно, например, через временную службу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="d4c88-186">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d4c88-187">Допускается следующее:</span><span class="sxs-lookup"><span data-stu-id="d4c88-187">It's fine to:</span></span>

<span data-ttu-id="d4c88-188">_ Разрешение одноэлементной службы из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="d4c88-189">Разрешение службы с заданной областью из другой службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="d4c88-190">По умолчанию в среде разработки разрешение службы из другой службы с более длинным временем существования вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="d4c88-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="d4c88-191">Дополнительные сведения см. в разделе [Проверка области](#sv).</span><span class="sxs-lookup"><span data-stu-id="d4c88-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="d4c88-192">Используйте службы с заданной областью в ПО промежуточного слоя, применяя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="d4c88-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="d4c88-193">Внедрите службу в метод `Invoke` или `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d4c88-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d4c88-194">С помощью [внедрите конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) создается исключение времени выполнения, поскольку оно заставляет службу с заданной областью вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="d4c88-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="d4c88-195">В примере в разделе [Параметры времени существования и регистрации](#lifetime-and-registration-options) демонстрируется подход `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="d4c88-196">Используйте [фабричное ПО промежуточного слоя](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="d4c88-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="d4c88-197">ПО промежуточного слоя, зарегистрированное с использованием этого подхода, активируется при каждом клиентском запросе (подключении), что позволяет внедрять службы с заданной областью в метод `InvokeAsync` ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d4c88-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="d4c88-198">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d4c88-199">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-199">Singleton</span></span>

<span data-ttu-id="d4c88-200">Одноэлементные службы времени существования создаются в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="d4c88-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="d4c88-201">При первом запросе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-201">The first time they're requested.</span></span>
* <span data-ttu-id="d4c88-202">Разработчиком при предоставлении экземпляра реализации непосредственно в контейнер.</span><span class="sxs-lookup"><span data-stu-id="d4c88-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="d4c88-203">Этот подход требуется достаточно редко.</span><span class="sxs-lookup"><span data-stu-id="d4c88-203">This approach is rarely needed.</span></span>

<span data-ttu-id="d4c88-204">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d4c88-205">Если в приложении нужно использовать одноэлементные службы, разрешите контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="d4c88-206">Не реализуйте одноэлементный подход и предоставьте код для удаления одноэлементных объектов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="d4c88-207">Службы никогда не должны удаляться кодом, который разрешил службу из контейнера.</span><span class="sxs-lookup"><span data-stu-id="d4c88-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="d4c88-208">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="d4c88-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="d4c88-209">Зарегистрируйте одноэлементные службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="d4c88-210">Одноэлементные службы должны быть потокобезопасными и часто использоваться в службах без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="d4c88-211">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="d4c88-212">Поскольку память не освобождается до завершения работы приложения, рекомендуется учитывать использование памяти одноэлементным объектом.</span><span class="sxs-lookup"><span data-stu-id="d4c88-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c88-213">Разрешать службу с заданной областью из одноэлементного объекта \* **запрещено** _.</span><span class="sxs-lookup"><span data-stu-id="d4c88-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d4c88-214">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="d4c88-215">Допускается разрешать одноэлементную службу из службы с заданной областью или временной службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d4c88-216">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-216">Service registration methods</span></span>

<span data-ttu-id="d4c88-217">Платформа предоставляет методы расширения регистрации службы, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="d4c88-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="d4c88-218">Метод</span><span class="sxs-lookup"><span data-stu-id="d4c88-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="d4c88-219">Автоматически</span><span class="sxs-lookup"><span data-stu-id="d4c88-219">Automatic</span></span><br><span data-ttu-id="d4c88-220">объект</span><span class="sxs-lookup"><span data-stu-id="d4c88-220">object</span></span><br><span data-ttu-id="d4c88-221">удаление</span><span class="sxs-lookup"><span data-stu-id="d4c88-221">disposal</span></span> | <span data-ttu-id="d4c88-222">Несколько</span><span class="sxs-lookup"><span data-stu-id="d4c88-222">Multiple</span></span><br><span data-ttu-id="d4c88-223">реализации</span><span class="sxs-lookup"><span data-stu-id="d4c88-223">implementations</span></span> | <span data-ttu-id="d4c88-224">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="d4c88-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d4c88-225">Пример.</span><span class="sxs-lookup"><span data-stu-id="d4c88-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="d4c88-226">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-226">Yes</span></span>                             | <span data-ttu-id="d4c88-227">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-227">Yes</span></span>                         | <span data-ttu-id="d4c88-228">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-229">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="d4c88-230">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-230">Yes</span></span>                             | <span data-ttu-id="d4c88-231">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-231">Yes</span></span>                         | <span data-ttu-id="d4c88-232">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d4c88-233">Пример.</span><span class="sxs-lookup"><span data-stu-id="d4c88-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="d4c88-234">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-234">Yes</span></span>                             | <span data-ttu-id="d4c88-235">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-235">No</span></span>                          | <span data-ttu-id="d4c88-236">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-237">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="d4c88-238">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-238">No</span></span>                              | <span data-ttu-id="d4c88-239">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-239">Yes</span></span>                         | <span data-ttu-id="d4c88-240">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-241">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="d4c88-242">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-242">No</span></span>                              | <span data-ttu-id="d4c88-243">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-243">No</span></span>                          | <span data-ttu-id="d4c88-244">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-244">Yes</span></span>       |

<span data-ttu-id="d4c88-245">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d4c88-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d4c88-246">Распространенный сценарий для использования нескольких реализаций — [макетирование типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d4c88-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d4c88-247">Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d4c88-248">Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d4c88-249">Эти методы могут регистрировать несколько _экземпляров\* службы, но все они будут иметь одинаковую *реализацию* типа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d4c88-250">Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d4c88-251">В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d4c88-252">Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d4c88-253">Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="d4c88-254">Платформа также предоставляет методы расширения `TryAdd{LIFETIME}`, которые регистрируют службу только в том случае, если реализация еще не зарегистрирована.</span><span class="sxs-lookup"><span data-stu-id="d4c88-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d4c88-255">В следующем примере вызов `AddSingleton` регистрирует `MyDependency` как реализацию для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="d4c88-256">Вызов `TryAddSingleton` ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="d4c88-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="d4c88-257">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="d4c88-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="d4c88-258">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="d4c88-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d4c88-259">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d4c88-260">При регистрации служб разработчику необходимо добавить экземпляр в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="d4c88-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d4c88-261">Как правило, авторы библиотек используют `TryAddEnumerable`, чтобы избежать регистрации нескольких копий реализации в контейнере.</span><span class="sxs-lookup"><span data-stu-id="d4c88-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="d4c88-262">В следующем примере первый вызов `TryAddEnumerable` регистрирует `MyDependency` как реализацию для `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="d4c88-263">Второй вызов регистрирует `MyDependency` для `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="d4c88-264">Третий вызов ничего не делает, поскольку у `IMyDependency1` уже есть зарегистрированная реализация `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="d4c88-265">Регистрация службы обычно не зависит от порядка, за исключением случаев регистрации нескольких реализаций одного типа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="d4c88-266">`IServiceCollection` является коллекцией объектов <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="d4c88-267">В следующем примере показано, как зарегистрировать службу, создав и добавив `ServiceDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="d4c88-268">Встроенные методы `Add{LIFETIME}` используют аналогичный подход.</span><span class="sxs-lookup"><span data-stu-id="d4c88-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="d4c88-269">Например, см. [исходный код для AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="d4c88-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d4c88-270">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="d4c88-270">Constructor injection behavior</span></span>

<span data-ttu-id="d4c88-271">Службы можно разрешать с помощью:</span><span class="sxs-lookup"><span data-stu-id="d4c88-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d4c88-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="d4c88-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="d4c88-273">Создает объекты, которые не зарегистрированы в контейнере.</span><span class="sxs-lookup"><span data-stu-id="d4c88-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="d4c88-274">Используется с функциями платформы, такими как [вспомогательные приложения для тегов](xref:mvc/views/tag-helpers/intro), контроллеры MVC и [средства привязки моделей](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d4c88-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="d4c88-275">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d4c88-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d4c88-276">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="d4c88-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d4c88-277">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="d4c88-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d4c88-278">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d4c88-279">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d4c88-279">Entity Framework contexts</span></span>

<span data-ttu-id="d4c88-280">По умолчанию контексты Entity Framework добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d4c88-281">Чтобы использовать другое время существования, укажите его с помощью перегрузки <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="d4c88-282">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d4c88-283">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="d4c88-283">Lifetime and registration options</span></span>

<span data-ttu-id="d4c88-284">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации службы, рассмотрим интерфейсы, представляющие задачу в виде операции с идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="d4c88-285">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d4c88-286">Следующий класс `Operation` реализует все предыдущие интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="d4c88-287">Конструктор `Operation` создает идентификатор GUID и сохраняет последние 4 символа в свойстве `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="d4c88-288">Метод `Startup.ConfigureServices` создает несколько регистраций класса `Operation` в соответствии с именованным временем существования:</span><span class="sxs-lookup"><span data-stu-id="d4c88-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d4c88-289">В примере приложения показано время существования объектов в пределах запросов и между запросами.</span><span class="sxs-lookup"><span data-stu-id="d4c88-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="d4c88-290">`IndexModel` и ПО промежуточного слоя запрашивают каждый тип `IOperation` и регистрируют `OperationId` для каждого из них:</span><span class="sxs-lookup"><span data-stu-id="d4c88-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d4c88-291">Аналогично `IndexModel`, ПО промежуточного слоя и разрешает те же службы:</span><span class="sxs-lookup"><span data-stu-id="d4c88-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="d4c88-292">Службы с заданной областью должны быть разрешены в методе `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="d4c88-293">Выходные данные средства ведения журнала содержат:</span><span class="sxs-lookup"><span data-stu-id="d4c88-293">The logger output shows:</span></span>

* <span data-ttu-id="d4c88-294">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="d4c88-294">*Transient* objects are always different.</span></span> <span data-ttu-id="d4c88-295">Значение временного `OperationId` отличается в `IndexModel` и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d4c88-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="d4c88-296">Объекты с *заданной областью* остаются неизменными в пределах одного запроса, но в разных запросах используются разные объекты.</span><span class="sxs-lookup"><span data-stu-id="d4c88-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="d4c88-297">*Одноэлементные* объекты одинаковы для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="d4c88-298">Чтобы уменьшить объем выводимых данных журнала, задайте в файле *appsettings.Development.json* параметр "Logging:LogLevel:Microsoft:Error".</span><span class="sxs-lookup"><span data-stu-id="d4c88-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="d4c88-299">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="d4c88-299">Call services from main</span></span>

<span data-ttu-id="d4c88-300">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d4c88-301">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="d4c88-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="d4c88-302">В следующем примере показано, как получить доступ к службе `IMyDependency` с заданной областью и вызвать ее метод `WriteMessage` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="d4c88-303">Проверка области</span><span class="sxs-lookup"><span data-stu-id="d4c88-303">Scope validation</span></span>

<span data-ttu-id="d4c88-304">Когда приложение выполняется в [среде разработки](xref:fundamentals/environments) и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="d4c88-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d4c88-305">Службы с заданной областью не разрешаются из корневого поставщика службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="d4c88-306">Службы с заданной областью не вводятся в одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="d4c88-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="d4c88-307">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="d4c88-308">Время существования корневого поставщика службы соответствует времени существования приложения — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d4c88-309">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="d4c88-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d4c88-310">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="d4c88-311">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d4c88-312">Дополнительные сведения см. в разделе [Проверка области](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d4c88-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="d4c88-313">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="d4c88-313">Request Services</span></span>

<span data-ttu-id="d4c88-314">Службы, доступные в пределах запроса ASP.NET Core, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d4c88-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="d4c88-315">При запросе в рамках запроса службы и их зависимости разрешаются из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="d4c88-316">Платформа создает область для каждого запроса, а `RequestServices` предоставляет поставщик услуг с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="d4c88-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="d4c88-317">Все службы с заданной областью действительны до тех пор, пока запрос активен.</span><span class="sxs-lookup"><span data-stu-id="d4c88-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="d4c88-318">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не разрешать службы из коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="d4c88-319">В результате создаются классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="d4c88-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d4c88-320">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-320">Design services for dependency injection</span></span>

<span data-ttu-id="d4c88-321">При разработке служб для внедрения зависимостей придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="d4c88-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="d4c88-322">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d4c88-323">Избегайте создания глобального состояния. Для этого проектируйте приложения для использования отдельных служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="d4c88-324">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d4c88-325">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="d4c88-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d4c88-326">Сделайте службы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="d4c88-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d4c88-327">Если класс имеет слишком много внедренных зависимостей, это может указывать на то, что у класса слишком много задач и он нарушает [принцип единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d4c88-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d4c88-328">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новые классы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="d4c88-329">Помните, что в классах модели страниц :::no-loc(Razor)::: Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="d4c88-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d4c88-330">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-330">Disposal of services</span></span>

<span data-ttu-id="d4c88-331">Контейнер вызывает <xref:System.IDisposable.Dispose%2A> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d4c88-332">Службы, разрешенные из контейнера, никогда не должны удаляться разработчиком.</span><span class="sxs-lookup"><span data-stu-id="d4c88-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="d4c88-333">Если тип или фабрика зарегистрированы как одноэлементный объект, контейнер автоматически удалит одноэлементные объекты.</span><span class="sxs-lookup"><span data-stu-id="d4c88-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="d4c88-334">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="d4c88-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d4c88-335">После каждого обновления страницы индекса в консоли отладки отображаются следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d4c88-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="d4c88-336">Службы, не созданные контейнером службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-336">Services not created by the service container</span></span>

<span data-ttu-id="d4c88-337">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="d4c88-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="d4c88-338">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="d4c88-338">In the preceding code:</span></span>

* <span data-ttu-id="d4c88-339">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d4c88-340">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4c88-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d4c88-341">За удаление служб отвечает разработчик.</span><span class="sxs-lookup"><span data-stu-id="d4c88-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d4c88-342">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="d4c88-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d4c88-343">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-343">Transient, limited lifetime</span></span>

<span data-ttu-id="d4c88-344">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="d4c88-344">**Scenario**</span></span>

<span data-ttu-id="d4c88-345">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="d4c88-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d4c88-346">Экземпляр разрешается в корневой области (в корневом контейнере).</span><span class="sxs-lookup"><span data-stu-id="d4c88-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="d4c88-347">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="d4c88-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d4c88-348">**Решение**</span><span class="sxs-lookup"><span data-stu-id="d4c88-348">**Solution**</span></span>

<span data-ttu-id="d4c88-349">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="d4c88-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d4c88-350">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d4c88-351">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="d4c88-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d4c88-352">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d4c88-353">Используйте <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d4c88-354">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="d4c88-355">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="d4c88-355">**Scenario**</span></span>

<span data-ttu-id="d4c88-356">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для экземпляра <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="d4c88-357">**Решение**</span><span class="sxs-lookup"><span data-stu-id="d4c88-357">**Solution**</span></span>

<span data-ttu-id="d4c88-358">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="d4c88-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="d4c88-359">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для создания нового <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d4c88-360">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d4c88-361">Удалите область, если она больше не нужна.</span><span class="sxs-lookup"><span data-stu-id="d4c88-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="d4c88-362">Общие рекомендации по IDisposable</span><span class="sxs-lookup"><span data-stu-id="d4c88-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="d4c88-363">Не регистрируйте экземпляры <xref:System.IDisposable> с временным временем существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="d4c88-364">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="d4c88-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d4c88-365">Не разрешайте экземпляры <xref:System.IDisposable> с временным временем существования или временем существования с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="d4c88-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="d4c88-366">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, но это не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="d4c88-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="d4c88-367">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d4c88-368">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="d4c88-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d4c88-369">Области должны использоваться для управления временем существования служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="d4c88-370">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="d4c88-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d4c88-371">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-371">Default service container replacement</span></span>

<span data-ttu-id="d4c88-372">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="d4c88-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d4c88-373">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="d4c88-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="d4c88-374">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="d4c88-374">Property injection</span></span>
* <span data-ttu-id="d4c88-375">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="d4c88-375">Injection based on name</span></span>
* <span data-ttu-id="d4c88-376">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="d4c88-376">Child containers</span></span>
* <span data-ttu-id="d4c88-377">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-377">Custom lifetime management</span></span>
* <span data-ttu-id="d4c88-378">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="d4c88-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d4c88-379">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="d4c88-379">Convention-based registration</span></span>

<span data-ttu-id="d4c88-380">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="d4c88-381">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="d4c88-381">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="d4c88-382">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-382">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="d4c88-383">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="d4c88-383">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="d4c88-384">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-384">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="d4c88-385">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="d4c88-385">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="d4c88-386">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-386">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="d4c88-387">Unity</span><span class="sxs-lookup"><span data-stu-id="d4c88-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="d4c88-388">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="d4c88-388">Thread safety</span></span>

<span data-ttu-id="d4c88-389">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="d4c88-390">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="d4c88-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="d4c88-391">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="d4c88-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d4c88-392">Как и конструктор типов (`static`), он гарантированно будет вызываться только один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="d4c88-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d4c88-393">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="d4c88-393">Recommendations</span></span>

* <span data-ttu-id="d4c88-394">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="d4c88-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="d4c88-395">Так как C# не поддерживает асинхронные конструкторы, следует использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="d4c88-396">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d4c88-397">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d4c88-398">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d4c88-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d4c88-399">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к другому объекту.</span><span class="sxs-lookup"><span data-stu-id="d4c88-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="d4c88-400">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d4c88-401">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-401">Avoid static access to services.</span></span> <span data-ttu-id="d4c88-402">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) в качестве статического поля или свойства.</span><span class="sxs-lookup"><span data-stu-id="d4c88-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="d4c88-403">Обеспечьте быстродействие и синхронизацию фабрик DI.</span><span class="sxs-lookup"><span data-stu-id="d4c88-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="d4c88-404">Старайтесь не использовать *схему указателя служб*.</span><span class="sxs-lookup"><span data-stu-id="d4c88-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d4c88-405">Например, не вызывайте <xref:System.IServiceProvider.GetService%2A> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d4c88-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d4c88-406">**Неправильно** :</span><span class="sxs-lookup"><span data-stu-id="d4c88-406">**Incorrect:**</span></span>

    ![Неверный код](dependency-injection/_static/bad.png)

  <span data-ttu-id="d4c88-408">**Правильно** :</span><span class="sxs-lookup"><span data-stu-id="d4c88-408">**Correct** :</span></span>

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
* <span data-ttu-id="d4c88-409">Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d4c88-410">Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d4c88-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="d4c88-411">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d4c88-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="d4c88-412">Избегайте вызовов <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="d4c88-413">Вызов `BuildServiceProvider` обычно происходит, когда разработчику необходимо разрешить службу в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="d4c88-414">Например, рассмотрим случай, когда `LoginPath` загружается из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d4c88-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="d4c88-415">Добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="d4c88-415">Avoid the following approach:</span></span>

  ![Неверный код при вызове BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="d4c88-417">На предыдущем рисунке при выборе строки, отмеченной зеленой волнистой линией в разделе `services.BuildServiceProvider`, отображается следующее предупреждение ASP0000:</span><span class="sxs-lookup"><span data-stu-id="d4c88-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="d4c88-418">ASP0000. Вызов BuildServiceProvider из кода приложения приводит к созданию дополнительной копии создаваемых одноэлементных служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="d4c88-419">В качестве параметров для Configure можно использовать альтернативные варианты, такие как службы внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="d4c88-420">При вызове `BuildServiceProvider` создается второй контейнер, который может создавать разорванные одноэлементные экземпляры и ссылаться на графы объектов в нескольких контейнерах.</span><span class="sxs-lookup"><span data-stu-id="d4c88-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="d4c88-421">Правильный способ получения `LoginPath` — использование встроенной поддержки шаблона параметров для внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d4c88-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="d4c88-422">Неудаляемые временные службы фиксируются контейнером для их удаления.</span><span class="sxs-lookup"><span data-stu-id="d4c88-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="d4c88-423">Это может привести к утечке памяти, если она разрешена из контейнера верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="d4c88-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="d4c88-424">Включите проверку области, чтобы убедиться, что в приложении нет отдельных объектов, записывающих службы с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="d4c88-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="d4c88-425">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d4c88-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d4c88-426">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="d4c88-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d4c88-427">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="d4c88-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d4c88-428">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d4c88-429">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d4c88-430">Рекомендуемые подходы к мультитенантности при внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d4c88-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) — это платформа приложений для создания модульных мультитенантных приложений в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4c88-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="d4c88-432">Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d4c88-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d4c88-433">Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. [здесь](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="d4c88-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d4c88-434">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-434">Framework-provided services</span></span>

<span data-ttu-id="d4c88-435">Метод `Startup.ConfigureServices` регистрирует службы, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d4c88-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d4c88-436">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="d4c88-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d4c88-437">Для приложений, основанных на шаблонах ASP.NET Core, платформа регистрирует более 250 служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="d4c88-438">В следующей таблице перечислены некоторые примеры этих зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="d4c88-439">Тип службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-439">Service Type</span></span>                                                                                    | <span data-ttu-id="d4c88-440">Время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d4c88-441">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="d4c88-442">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="d4c88-443">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="d4c88-444">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="d4c88-445">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="d4c88-446">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="d4c88-447">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="d4c88-448">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="d4c88-449">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="d4c88-450">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="d4c88-451">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="d4c88-452">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="d4c88-453">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="d4c88-454">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d4c88-455">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d4c88-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="d4c88-456">Шаблоны конференций NDC для разработки приложений с внедрением зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d4c88-457">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4c88-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d4c88-458">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d4c88-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d4c88-459">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d4c88-460">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="d4c88-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="d4c88-461">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d4c88-461">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4c88-462">Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler)</span><span class="sxs-lookup"><span data-stu-id="d4c88-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="d4c88-463">ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.</span><span class="sxs-lookup"><span data-stu-id="d4c88-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d4c88-464">Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d4c88-465">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4c88-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d4c88-466">Общие сведения о внедрении зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-466">Overview of dependency injection</span></span>

<span data-ttu-id="d4c88-467">*Зависимость* — это любой объект, который требуется другому объекту.</span><span class="sxs-lookup"><span data-stu-id="d4c88-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d4c88-468">Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.</span><span class="sxs-lookup"><span data-stu-id="d4c88-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d4c88-469">Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d4c88-470">Класс `MyDependency` выступает зависимостью класса `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d4c88-471">Этот класс создает экземпляр `MyDependency` и напрямую зависит от него.</span><span class="sxs-lookup"><span data-stu-id="d4c88-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d4c88-472">Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="d4c88-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d4c88-473">Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.</span><span class="sxs-lookup"><span data-stu-id="d4c88-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d4c88-474">Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.</span><span class="sxs-lookup"><span data-stu-id="d4c88-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d4c88-475">В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению.</span><span class="sxs-lookup"><span data-stu-id="d4c88-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d4c88-476">Такая реализация плохо подходит для модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d4c88-477">В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.</span><span class="sxs-lookup"><span data-stu-id="d4c88-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d4c88-478">Внедрение зависимостей устраняет эти проблемы следующим образом:</span><span class="sxs-lookup"><span data-stu-id="d4c88-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d4c88-479">Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d4c88-480">Зависимость регистрируется в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d4c88-481">ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d4c88-482">Службы регистрируются в приложении в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d4c88-483">Служба *внедряется* в конструктор класса там, где он используется.</span><span class="sxs-lookup"><span data-stu-id="d4c88-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d4c88-484">Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен.</span><span class="sxs-lookup"><span data-stu-id="d4c88-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d4c88-485">В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению.</span><span class="sxs-lookup"><span data-stu-id="d4c88-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d4c88-486">Этот интерфейс реализуется конкретным типом, `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d4c88-487">`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d4c88-488">Использование цепочки внедрений зависимостей не является чем-то необычным.</span><span class="sxs-lookup"><span data-stu-id="d4c88-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d4c88-489">Каждая запрашиваемая зависимость запрашивает собственные зависимости.</span><span class="sxs-lookup"><span data-stu-id="d4c88-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d4c88-490">Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d4c88-491">Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей* , *графом зависимостей* или *графом объектов*.</span><span class="sxs-lookup"><span data-stu-id="d4c88-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="d4c88-492">`IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d4c88-493">`IMyDependency` регистрируется в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c88-494">Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d4c88-495">Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="d4c88-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d4c88-496">В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d4c88-497">Регистрация регулирует время существования службы согласно времени существования одного запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d4c88-498">Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="d4c88-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d4c88-499">Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d4c88-500">Например, `services.AddMvc()` добавляет службы :::no-loc(Razor):::, которые нужны для Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="d4c88-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="d4c88-501">Рекомендуем придерживаться такого подхода в приложениях.</span><span class="sxs-lookup"><span data-stu-id="d4c88-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d4c88-502">Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d4c88-503">Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d4c88-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d4c88-504">Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.</span><span class="sxs-lookup"><span data-stu-id="d4c88-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d4c88-505">Поле используется во всем классе для доступа к службе (по мере необходимости).</span><span class="sxs-lookup"><span data-stu-id="d4c88-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d4c88-506">В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d4c88-507">Службы, внедренные в конструктор Startup</span><span class="sxs-lookup"><span data-stu-id="d4c88-507">Services injected into Startup</span></span>

<span data-ttu-id="d4c88-508">При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб:</span><span class="sxs-lookup"><span data-stu-id="d4c88-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d4c88-509">Службы можно внедрить в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d4c88-510">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d4c88-511">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-511">Framework-provided services</span></span>

<span data-ttu-id="d4c88-512">Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d4c88-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d4c88-513">Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)).</span><span class="sxs-lookup"><span data-stu-id="d4c88-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d4c88-514">Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой.</span><span class="sxs-lookup"><span data-stu-id="d4c88-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d4c88-515">В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d4c88-516">Тип службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-516">Service Type</span></span> | <span data-ttu-id="d4c88-517">Время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d4c88-518">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d4c88-519">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d4c88-520">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d4c88-521">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d4c88-522">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d4c88-523">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d4c88-524">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d4c88-525">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d4c88-526">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d4c88-527">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d4c88-528">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d4c88-529">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d4c88-530">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d4c88-531">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d4c88-532">Регистрация дополнительных служб с помощью методов расширения</span><span class="sxs-lookup"><span data-stu-id="d4c88-532">Register additional services with extension methods</span></span>

<span data-ttu-id="d4c88-533">Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d4c88-534">Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span><span class="sxs-lookup"><span data-stu-id="d4c88-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="d4c88-535">Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d4c88-536">Время существования служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-536">Service lifetimes</span></span>

<span data-ttu-id="d4c88-537">Для каждой зарегистрированной службы выбирайте подходящее время существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d4c88-538">Для служб ASP.NET можно настроить следующие параметры времени существования:</span><span class="sxs-lookup"><span data-stu-id="d4c88-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d4c88-539">Временный</span><span class="sxs-lookup"><span data-stu-id="d4c88-539">Transient</span></span>

<span data-ttu-id="d4c88-540">Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d4c88-541">Это время существования лучше всего подходит для простых служб без отслеживания состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="d4c88-542">В приложениях, обрабатывающих запросы, временные службы удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="d4c88-543">Область действия</span><span class="sxs-lookup"><span data-stu-id="d4c88-543">Scoped</span></span>

<span data-ttu-id="d4c88-544">Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения).</span><span class="sxs-lookup"><span data-stu-id="d4c88-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="d4c88-545">В приложениях, обрабатывающих запросы, службы с заданной областью удаляются в конце запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c88-546">При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d4c88-547">Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект.</span><span class="sxs-lookup"><span data-stu-id="d4c88-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d4c88-548">Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d4c88-549">Одноэлементный</span><span class="sxs-lookup"><span data-stu-id="d4c88-549">Singleton</span></span>

<span data-ttu-id="d4c88-550">Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).</span><span class="sxs-lookup"><span data-stu-id="d4c88-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d4c88-551">Созданный экземпляр используют все последующие запросы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d4c88-552">Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d4c88-553">Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="d4c88-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="d4c88-554">В приложениях, обрабатывающих запросы, отдельные службы удаляются, когда <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> удаляется по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c88-555">Разрешать регулируемую службу из одиночной нельзя.</span><span class="sxs-lookup"><span data-stu-id="d4c88-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d4c88-556">При обработке последующих запросов это может вызвать неправильное состояние службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d4c88-557">Методы регистрации службы</span><span class="sxs-lookup"><span data-stu-id="d4c88-557">Service registration methods</span></span>

<span data-ttu-id="d4c88-558">Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="d4c88-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d4c88-559">Метод</span><span class="sxs-lookup"><span data-stu-id="d4c88-559">Method</span></span> | <span data-ttu-id="d4c88-560">Автоматически</span><span class="sxs-lookup"><span data-stu-id="d4c88-560">Automatic</span></span><br><span data-ttu-id="d4c88-561">объект</span><span class="sxs-lookup"><span data-stu-id="d4c88-561">object</span></span><br><span data-ttu-id="d4c88-562">удаление</span><span class="sxs-lookup"><span data-stu-id="d4c88-562">disposal</span></span> | <span data-ttu-id="d4c88-563">Несколько</span><span class="sxs-lookup"><span data-stu-id="d4c88-563">Multiple</span></span><br><span data-ttu-id="d4c88-564">реализации</span><span class="sxs-lookup"><span data-stu-id="d4c88-564">implementations</span></span> | <span data-ttu-id="d4c88-565">Передача аргументов</span><span class="sxs-lookup"><span data-stu-id="d4c88-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d4c88-566">Пример.</span><span class="sxs-lookup"><span data-stu-id="d4c88-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="d4c88-567">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-567">Yes</span></span> | <span data-ttu-id="d4c88-568">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-568">Yes</span></span> | <span data-ttu-id="d4c88-569">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-570">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d4c88-571">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-571">Yes</span></span> | <span data-ttu-id="d4c88-572">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-572">Yes</span></span> | <span data-ttu-id="d4c88-573">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d4c88-574">Пример.</span><span class="sxs-lookup"><span data-stu-id="d4c88-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="d4c88-575">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-575">Yes</span></span> | <span data-ttu-id="d4c88-576">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-576">No</span></span> | <span data-ttu-id="d4c88-577">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-578">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d4c88-579">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-579">No</span></span> | <span data-ttu-id="d4c88-580">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-580">Yes</span></span> | <span data-ttu-id="d4c88-581">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="d4c88-582">Примеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="d4c88-583">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-583">No</span></span> | <span data-ttu-id="d4c88-584">Нет</span><span class="sxs-lookup"><span data-stu-id="d4c88-584">No</span></span> | <span data-ttu-id="d4c88-585">Да</span><span class="sxs-lookup"><span data-stu-id="d4c88-585">Yes</span></span> |

<span data-ttu-id="d4c88-586">Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d4c88-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d4c88-587">Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d4c88-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d4c88-588">Регистрация службы только с типом реализации эквивалентна регистрации этой службы с той же реализацией и типом службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="d4c88-589">Именно поэтому несколько реализаций службы не могут быть зарегистрированы с помощью методов, которые не принимают явный тип службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="d4c88-590">Эти методы могут регистрировать несколько *экземпляров* службы, но все они будут иметь одинаковую *реализацию* типа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="d4c88-591">Любой из указанных выше методов регистрации службы можно использовать для регистрации нескольких экземпляров службы одного типа службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="d4c88-592">В следующем примере метод `AddSingleton` вызывается дважды с типом службы `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="d4c88-593">Второй вызов `AddSingleton` переопределяет предыдущий, если он разрешается как `IMyDependency`, и добавляет к предыдущему, если несколько служб разрешаются через `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="d4c88-594">Службы отображаются в том порядке, в котором они были зарегистрированы при разрешении через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="d4c88-595">Платформа также предоставляет методы расширения `TryAdd{LIFETIME}`, которые регистрируют службу только в том случае, если реализация еще не зарегистрирована.</span><span class="sxs-lookup"><span data-stu-id="d4c88-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d4c88-596">В следующем примере вызов `AddSingleton` регистрирует `MyDependency` как реализацию для `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="d4c88-597">Вызов `TryAddSingleton` ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="d4c88-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="d4c88-598">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="d4c88-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d4c88-599">Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*.</span><span class="sxs-lookup"><span data-stu-id="d4c88-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d4c88-600">Несколько служб разрешается через `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d4c88-601">При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.</span><span class="sxs-lookup"><span data-stu-id="d4c88-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d4c88-602">Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.</span><span class="sxs-lookup"><span data-stu-id="d4c88-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d4c88-603">В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d4c88-604">Вторая строка регистрирует `MyDep` для `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d4c88-605">Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d4c88-606">Поведение внедрения через конструктор</span><span class="sxs-lookup"><span data-stu-id="d4c88-606">Constructor injection behavior</span></span>

<span data-ttu-id="d4c88-607">Службы можно разрешать двумя методами:</span><span class="sxs-lookup"><span data-stu-id="d4c88-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d4c88-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d4c88-609">`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d4c88-610">Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d4c88-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d4c88-611">Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор.</span><span class="sxs-lookup"><span data-stu-id="d4c88-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d4c88-612">Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.</span><span class="sxs-lookup"><span data-stu-id="d4c88-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d4c88-613">Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d4c88-614">Контексты Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d4c88-614">Entity Framework contexts</span></span>

<span data-ttu-id="d4c88-615">Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.</span><span class="sxs-lookup"><span data-stu-id="d4c88-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d4c88-616">Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="d4c88-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d4c88-617">Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d4c88-618">Параметры времени существования и регистрации</span><span class="sxs-lookup"><span data-stu-id="d4c88-618">Lifetime and registration options</span></span>

<span data-ttu-id="d4c88-619">Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d4c88-620">В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d4c88-621">Интерфейсы реализованы в классе `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d4c88-622">Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.</span><span class="sxs-lookup"><span data-stu-id="d4c88-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d4c88-623">Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d4c88-624">Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d4c88-625">Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d4c88-626">`OperationService` получает новый экземпляр класса `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d4c88-627">Новый экземпляр возвращает другой идентификатор `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d4c88-628">Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d4c88-629">В разных клиентских запросах обе службы используют разные значения `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d4c88-630">Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d4c88-631">В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d4c88-632">Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d4c88-633">Понятно, когда этот тип используется (его GUID — все нули).</span><span class="sxs-lookup"><span data-stu-id="d4c88-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d4c88-634">В примере приложения показано время существования объектов в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d4c88-635">В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d4c88-636">После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.</span><span class="sxs-lookup"><span data-stu-id="d4c88-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d4c88-637">Результаты двух запросов будут выглядеть так:</span><span class="sxs-lookup"><span data-stu-id="d4c88-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d4c88-638">**Первый запрос**</span><span class="sxs-lookup"><span data-stu-id="d4c88-638">**First request:**</span></span>

<span data-ttu-id="d4c88-639">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="d4c88-639">Controller operations:</span></span>

<span data-ttu-id="d4c88-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d4c88-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d4c88-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d4c88-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d4c88-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d4c88-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d4c88-643">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d4c88-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d4c88-644">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-644">`OperationService` operations:</span></span>

<span data-ttu-id="d4c88-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d4c88-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d4c88-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d4c88-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d4c88-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d4c88-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d4c88-648">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d4c88-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d4c88-649">**Второй запрос**</span><span class="sxs-lookup"><span data-stu-id="d4c88-649">**Second request:**</span></span>

<span data-ttu-id="d4c88-650">Операции контроллера:</span><span class="sxs-lookup"><span data-stu-id="d4c88-650">Controller operations:</span></span>

<span data-ttu-id="d4c88-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d4c88-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d4c88-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d4c88-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d4c88-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d4c88-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d4c88-654">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d4c88-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d4c88-655">Операции `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-655">`OperationService` operations:</span></span>

<span data-ttu-id="d4c88-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d4c88-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d4c88-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d4c88-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d4c88-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d4c88-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d4c88-659">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d4c88-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d4c88-660">Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d4c88-661">*Временные* объекты всегда разные.</span><span class="sxs-lookup"><span data-stu-id="d4c88-661">*Transient* objects are always different.</span></span> <span data-ttu-id="d4c88-662">Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d4c88-663">Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр.</span><span class="sxs-lookup"><span data-stu-id="d4c88-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d4c88-664">*Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.</span><span class="sxs-lookup"><span data-stu-id="d4c88-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d4c88-665">*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d4c88-666">Вызов служб из функции main</span><span class="sxs-lookup"><span data-stu-id="d4c88-666">Call services from main</span></span>

<span data-ttu-id="d4c88-667">Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d4c88-668">Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.</span><span class="sxs-lookup"><span data-stu-id="d4c88-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d4c88-669">В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d4c88-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d4c88-670">Проверка области</span><span class="sxs-lookup"><span data-stu-id="d4c88-670">Scope validation</span></span>

<span data-ttu-id="d4c88-671">Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее:</span><span class="sxs-lookup"><span data-stu-id="d4c88-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d4c88-672">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="d4c88-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d4c88-673">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="d4c88-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d4c88-674">Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d4c88-675">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="d4c88-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d4c88-676">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="d4c88-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d4c88-677">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="d4c88-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d4c88-678">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d4c88-679">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="d4c88-680">Службы запросов</span><span class="sxs-lookup"><span data-stu-id="d4c88-680">Request Services</span></span>

<span data-ttu-id="d4c88-681">Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d4c88-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d4c88-682">Службы запросов — это все настроенные и запрашиваемые в приложении службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d4c88-683">Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d4c88-684">Как правило, использовать эти свойства в приложении напрямую не следует.</span><span class="sxs-lookup"><span data-stu-id="d4c88-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d4c88-685">Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.</span><span class="sxs-lookup"><span data-stu-id="d4c88-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d4c88-686">Этот процесс создает классы, которые легче тестировать.</span><span class="sxs-lookup"><span data-stu-id="d4c88-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d4c88-687">Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d4c88-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d4c88-688">Проектирование служб для внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-688">Design services for dependency injection</span></span>

<span data-ttu-id="d4c88-689">Придерживайтесь следующих рекомендаций:</span><span class="sxs-lookup"><span data-stu-id="d4c88-689">Best practices are to:</span></span>

* <span data-ttu-id="d4c88-690">Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d4c88-691">Избегайте статических классов и членов с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d4c88-692">Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния.</span><span class="sxs-lookup"><span data-stu-id="d4c88-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d4c88-693">Избегайте прямого создания экземпляров зависимых классов внутри служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d4c88-694">Прямое создание экземпляров обязывает использовать в коде определенную реализацию.</span><span class="sxs-lookup"><span data-stu-id="d4c88-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d4c88-695">Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.</span><span class="sxs-lookup"><span data-stu-id="d4c88-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d4c88-696">Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d4c88-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d4c88-697">Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.</span><span class="sxs-lookup"><span data-stu-id="d4c88-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d4c88-698">Помните, что в классах модели страниц :::no-loc(Razor)::: Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="d4c88-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d4c88-699">Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d4c88-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d4c88-700">Удаление служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-700">Disposal of services</span></span>

<span data-ttu-id="d4c88-701">Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d4c88-702">Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4c88-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d4c88-703">В следующем примере службы создаются контейнером службы и автоматически удаляются:</span><span class="sxs-lookup"><span data-stu-id="d4c88-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="d4c88-704">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d4c88-704">In the following example:</span></span>

* <span data-ttu-id="d4c88-705">Экземпляры службы не создаются контейнером службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d4c88-706">Платформе неизвестно предполагаемое время жизни службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d4c88-707">Платформа не удаляет службы автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4c88-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d4c88-708">Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="d4c88-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="d4c88-709">Руководство по применению временных и общих экземпляров IDisposable</span><span class="sxs-lookup"><span data-stu-id="d4c88-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="d4c88-710">Временный экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-710">Transient, limited lifetime</span></span>

<span data-ttu-id="d4c88-711">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="d4c88-711">**Scenario**</span></span>

<span data-ttu-id="d4c88-712">Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="d4c88-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="d4c88-713">Экземпляр разрешается в корневой области.</span><span class="sxs-lookup"><span data-stu-id="d4c88-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="d4c88-714">Экземпляр должен быть удален до завершения области.</span><span class="sxs-lookup"><span data-stu-id="d4c88-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="d4c88-715">**Решение**</span><span class="sxs-lookup"><span data-stu-id="d4c88-715">**Solution**</span></span>

<span data-ttu-id="d4c88-716">Используйте шаблон фабрики для создания экземпляра за пределами родительской области.</span><span class="sxs-lookup"><span data-stu-id="d4c88-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="d4c88-717">В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.</span><span class="sxs-lookup"><span data-stu-id="d4c88-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="d4c88-718">Если окончательный тип имеет другие зависимости, фабрика позволяет:</span><span class="sxs-lookup"><span data-stu-id="d4c88-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="d4c88-719">Получить <xref:System.IServiceProvider> в своем конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d4c88-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="d4c88-720">Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="d4c88-721">Общий экземпляр, ограниченное время существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="d4c88-722">**Сценарий**</span><span class="sxs-lookup"><span data-stu-id="d4c88-722">**Scenario**</span></span>

<span data-ttu-id="d4c88-723">Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="d4c88-724">**Решение**</span><span class="sxs-lookup"><span data-stu-id="d4c88-724">**Solution**</span></span>

<span data-ttu-id="d4c88-725">Зарегистрируйте экземпляр с временем существования с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="d4c88-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="d4c88-726">Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="d4c88-727">Используйте <xref:System.IServiceProvider> области для получения необходимых служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="d4c88-728">Удалить область по истечении времени существования.</span><span class="sxs-lookup"><span data-stu-id="d4c88-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="d4c88-729">Общие рекомендации</span><span class="sxs-lookup"><span data-stu-id="d4c88-729">General Guidelines</span></span>

* <span data-ttu-id="d4c88-730">Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.</span><span class="sxs-lookup"><span data-stu-id="d4c88-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="d4c88-731">Вместо этого используйте шаблон фабрики.</span><span class="sxs-lookup"><span data-stu-id="d4c88-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="d4c88-732">Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.</span><span class="sxs-lookup"><span data-stu-id="d4c88-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="d4c88-733">Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.</span><span class="sxs-lookup"><span data-stu-id="d4c88-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="d4c88-734">Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="d4c88-735">Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.</span><span class="sxs-lookup"><span data-stu-id="d4c88-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="d4c88-736">Области должны использоваться для управления жизненным циклом служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="d4c88-737">Области не являются иерархическими, и между ними нет специальной связи.</span><span class="sxs-lookup"><span data-stu-id="d4c88-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d4c88-738">Замена стандартного контейнера служб</span><span class="sxs-lookup"><span data-stu-id="d4c88-738">Default service container replacement</span></span>

<span data-ttu-id="d4c88-739">Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="d4c88-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d4c88-740">Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:</span><span class="sxs-lookup"><span data-stu-id="d4c88-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d4c88-741">Внедрение свойств</span><span class="sxs-lookup"><span data-stu-id="d4c88-741">Property injection</span></span>
* <span data-ttu-id="d4c88-742">Внедрение по имени</span><span class="sxs-lookup"><span data-stu-id="d4c88-742">Injection based on name</span></span>
* <span data-ttu-id="d4c88-743">Дочерние контейнеры</span><span class="sxs-lookup"><span data-stu-id="d4c88-743">Child containers</span></span>
* <span data-ttu-id="d4c88-744">Настраиваемое управление временем существования</span><span class="sxs-lookup"><span data-stu-id="d4c88-744">Custom lifetime management</span></span>
* <span data-ttu-id="d4c88-745">`Func<T>` поддерживает отложенную инициализацию</span><span class="sxs-lookup"><span data-stu-id="d4c88-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d4c88-746">Регистрация на основе соглашения</span><span class="sxs-lookup"><span data-stu-id="d4c88-746">Convention-based registration</span></span>

<span data-ttu-id="d4c88-747">С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:</span><span class="sxs-lookup"><span data-stu-id="d4c88-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* <span data-ttu-id="d4c88-748">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);</span><span class="sxs-lookup"><span data-stu-id="d4c88-748">[Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)</span></span>
* <span data-ttu-id="d4c88-749">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-749">[DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="d4c88-750">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);</span><span class="sxs-lookup"><span data-stu-id="d4c88-750">[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)</span></span>
* <span data-ttu-id="d4c88-751">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-751">[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)</span></span>
* <span data-ttu-id="d4c88-752">[Lamar](https://jasperfx.github.io/lamar/);</span><span class="sxs-lookup"><span data-stu-id="d4c88-752">[Lamar](https://jasperfx.github.io/lamar/)</span></span>
* <span data-ttu-id="d4c88-753">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection);</span><span class="sxs-lookup"><span data-stu-id="d4c88-753">[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)</span></span>
* [<span data-ttu-id="d4c88-754">Unity</span><span class="sxs-lookup"><span data-stu-id="d4c88-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d4c88-755">Потокобезопасность</span><span class="sxs-lookup"><span data-stu-id="d4c88-755">Thread safety</span></span>

<span data-ttu-id="d4c88-756">Создавайте потокобезопасные одноэлементные службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="d4c88-757">Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.</span><span class="sxs-lookup"><span data-stu-id="d4c88-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d4c88-758">Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="d4c88-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d4c88-759">Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком.</span><span class="sxs-lookup"><span data-stu-id="d4c88-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d4c88-760">Рекомендации</span><span class="sxs-lookup"><span data-stu-id="d4c88-760">Recommendations</span></span>

* <span data-ttu-id="d4c88-761">Разрешение служб на основе `async/await` и `Task` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="d4c88-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d4c88-762">C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.</span><span class="sxs-lookup"><span data-stu-id="d4c88-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d4c88-763">Не храните данные и конфигурацию непосредственно в контейнере служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d4c88-764">Например, обычно не следует добавлять корзину пользователя в контейнер служб.</span><span class="sxs-lookup"><span data-stu-id="d4c88-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d4c88-765">Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d4c88-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d4c88-766">Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.</span><span class="sxs-lookup"><span data-stu-id="d4c88-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d4c88-767">Лучше запросить фактический элемент через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d4c88-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="d4c88-768">Избегайте статического доступа к службам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-768">Avoid static access to services.</span></span> <span data-ttu-id="d4c88-769">Например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices).</span><span class="sxs-lookup"><span data-stu-id="d4c88-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="d4c88-770">Старайтесь не использовать *шаблон обнаружения служб* , который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d4c88-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="d4c88-771">Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d4c88-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d4c88-772">**Неправильно** :</span><span class="sxs-lookup"><span data-stu-id="d4c88-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="d4c88-773">**Правильно** :</span><span class="sxs-lookup"><span data-stu-id="d4c88-773">**Correct** :</span></span>

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

* <span data-ttu-id="d4c88-774">Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="d4c88-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="d4c88-775">Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d4c88-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d4c88-776">Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.</span><span class="sxs-lookup"><span data-stu-id="d4c88-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d4c88-777">Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.</span><span class="sxs-lookup"><span data-stu-id="d4c88-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d4c88-778">Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d4c88-779">Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.</span><span class="sxs-lookup"><span data-stu-id="d4c88-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4c88-780">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d4c88-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d4c88-781">Четыре способа удаления интерфейсов IDisposable в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4c88-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="d4c88-782">Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d4c88-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="d4c88-783">Принцип явных зависимостей</span><span class="sxs-lookup"><span data-stu-id="d4c88-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d4c88-784">Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)</span><span class="sxs-lookup"><span data-stu-id="d4c88-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="d4c88-785">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d4c88-785">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
