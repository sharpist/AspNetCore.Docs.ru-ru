---
title: "title: 'Внедрение зависимостей ASP.NET Core Blazor' author: guardrex description: 'Узнайте, как приложения Blazor могут внедрять службы в компоненты.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 19.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: d5b0747fb0505499197d1751511600bd91fed41d
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271787"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="5c518-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5c518-103">'Blazor'</span></span>

<span data-ttu-id="5c518-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5c518-104">'Identity'</span></span>

<span data-ttu-id="5c518-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5c518-105">'Let's Encrypt'</span></span> <span data-ttu-id="5c518-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5c518-106">'Razor'</span></span> <span data-ttu-id="5c518-107">'SignalR' uid: blazor/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="5c518-107">'SignalR' uid: blazor/dependency-injection</span></span>

<span data-ttu-id="5c518-108">Внедрение зависимостей Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c518-108">ASP.NET Core Blazor dependency injection</span></span> <span data-ttu-id="5c518-109">Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)</span><span class="sxs-lookup"><span data-stu-id="5c518-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

* Blazor<span data-ttu-id="5c518-110"> поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5c518-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="5c518-111">Приложения могут использовать встроенные службы, внедряя их в компоненты.</span><span class="sxs-lookup"><span data-stu-id="5c518-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="5c518-112">Приложения также могут определять и регистрировать пользовательские службы и предоставлять к ним доступ в рамках всего приложения с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5c518-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span> <span data-ttu-id="5c518-113">Внедрение зависимостей — это методика доступа к службам, настроенным в центральном расположении.</span><span class="sxs-lookup"><span data-stu-id="5c518-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="5c518-114">Она используется в приложениях Blazor для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="5c518-114">This can be useful in Blazor apps to:</span></span> <span data-ttu-id="5c518-115">Совместное использование одного экземпляра класса службы во множестве компонентов, называемого *одноэлементной* службой.</span><span class="sxs-lookup"><span data-stu-id="5c518-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>

## <a name="default-services"></a><span data-ttu-id="5c518-116">Отделение компонентов от конкретных классов служб с помощью абстракций ссылок.</span><span class="sxs-lookup"><span data-stu-id="5c518-116">Decouple components from concrete service classes by using reference abstractions.</span></span>

<span data-ttu-id="5c518-117">Например, рассмотрим интерфейс `IDataAccess` для доступа к данным в приложении.</span><span class="sxs-lookup"><span data-stu-id="5c518-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span>

| <span data-ttu-id="5c518-118">Этот интерфейс реализуется конкретным классом `DataAccess` и регистрируется в качестве службы в контейнере службы приложения.</span><span class="sxs-lookup"><span data-stu-id="5c518-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> | <span data-ttu-id="5c518-119">Когда компонент использует внедрение зависимостей для получения реализации `IDataAccess`, этот компонент не связывается с конкретным типом.</span><span class="sxs-lookup"><span data-stu-id="5c518-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> | <span data-ttu-id="5c518-120">Реализация может быть переключена, например, для реализации макета в модульных тестах.</span><span class="sxs-lookup"><span data-stu-id="5c518-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="5c518-121">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5c518-121">Default services</span></span> | <span data-ttu-id="5c518-122">Службы по умолчанию автоматически добавляются в коллекцию служб приложения.</span><span class="sxs-lookup"><span data-stu-id="5c518-122">Default services are automatically added to the app's service collection.</span></span><br><br><span data-ttu-id="5c518-123">Служба</span><span class="sxs-lookup"><span data-stu-id="5c518-123">Service</span></span><br><br><span data-ttu-id="5c518-124">Время существования</span><span class="sxs-lookup"><span data-stu-id="5c518-124">Lifetime</span></span> <span data-ttu-id="5c518-125">Описание</span><span class="sxs-lookup"><span data-stu-id="5c518-125">Description</span></span><br><br><span data-ttu-id="5c518-126">Временный</span><span class="sxs-lookup"><span data-stu-id="5c518-126">Transient</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="5c518-127">Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.</span><span class="sxs-lookup"><span data-stu-id="5c518-127">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><span data-ttu-id="5c518-128">Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме.</span><span class="sxs-lookup"><span data-stu-id="5c518-128">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span> | <span data-ttu-id="5c518-129">Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, по умолчанию настроенный в качестве службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-129">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="5c518-130">Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5c518-130">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="5c518-131">Для получения дополнительной информации см. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="5c518-131">For more information, see <xref:blazor/call-web-api>.</span></span><br><span data-ttu-id="5c518-132">Отдельная (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="5c518-132">Singleton (Blazor WebAssembly)</span></span> | <span data-ttu-id="5c518-133">С областью действия (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="5c518-133">Scoped (Blazor Server)</span></span> <span data-ttu-id="5c518-134">Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5c518-134">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> |

<span data-ttu-id="5c518-135">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="5c518-135">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="5c518-136">Отдельная (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="5c518-136">Singleton (Blazor WebAssembly)</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="5c518-137">С областью действия (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="5c518-137">Scoped (Blazor Server)</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="5c518-138">Содержит вспомогательные методы для работы с URI и состоянием навигации.</span><span class="sxs-lookup"><span data-stu-id="5c518-138">Contains helpers for working with URIs and navigation state.</span></span>

<span data-ttu-id="5c518-139">Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="5c518-139">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> <span data-ttu-id="5c518-140">Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически.</span><span class="sxs-lookup"><span data-stu-id="5c518-140">A custom service provider doesn't automatically provide the default services listed in the table.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="5c518-141">Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг.</span><span class="sxs-lookup"><span data-stu-id="5c518-141">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span> <span data-ttu-id="5c518-142">Добавление служб в приложение</span><span class="sxs-lookup"><span data-stu-id="5c518-142">Add services to an app</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Blazor<span data-ttu-id="5c518-143"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5c518-143"> WebAssembly</span></span> <span data-ttu-id="5c518-144">Настройте службы для коллекции служб приложения в методе `Main` файла *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5c518-144">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="5c518-145">В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="5c518-145">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

<span data-ttu-id="5c518-146">После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов.</span><span class="sxs-lookup"><span data-stu-id="5c518-146">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="5c518-147">Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого:</span><span class="sxs-lookup"><span data-stu-id="5c518-147">This can be useful for running initialization logic before rendering content:</span></span> <span data-ttu-id="5c518-148">Узел также предоставляет центральный экземпляр конфигурации для приложения.</span><span class="sxs-lookup"><span data-stu-id="5c518-148">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="5c518-149">Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, *appsettings.json*) в `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="5c518-149">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="5c518-150">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="5c518-150">Blazor Server</span></span>

<span data-ttu-id="5c518-151">После создания приложения изучите метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c518-151">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

| <span data-ttu-id="5c518-152">В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов дескриптора службы (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="5c518-152">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> | <span data-ttu-id="5c518-153">Службы добавляются путем предоставления дескрипторов служб в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="5c518-153">Services are added by providing service descriptors to the service collection.</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="5c518-154">В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`.</span><span class="sxs-lookup"><span data-stu-id="5c518-154">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span> <span data-ttu-id="5c518-155">Время существования службы</span><span class="sxs-lookup"><span data-stu-id="5c518-155">Service lifetime</span></span> <span data-ttu-id="5c518-156">Для служб можно настроить параметры времени существования, указанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5c518-156">Services can be configured with the lifetimes shown in the following table.</span></span> <span data-ttu-id="5c518-157">Время существования</span><span class="sxs-lookup"><span data-stu-id="5c518-157">Lifetime</span></span> <span data-ttu-id="5c518-158">Описание</span><span class="sxs-lookup"><span data-stu-id="5c518-158">Description</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="5c518-159">Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует.</span><span class="sxs-lookup"><span data-stu-id="5c518-159">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="5c518-160">Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="5c518-160">`Scoped`-registered services behave like `Singleton` services.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="5c518-161">Однако модель размещения сервера Blazor поддерживает время существования `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="5c518-161">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> |

<span data-ttu-id="5c518-162">В приложениях Blazor Server регистрация службы с заданной областью ограничивается *соединением*.</span><span class="sxs-lookup"><span data-stu-id="5c518-162">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="5c518-163">По этой причине использование служб с заданной областью предпочтительно для служб, которые должны быть ограничены текущим пользователем, даже если текущим намерением является запуск на стороне клиента в браузере.</span><span class="sxs-lookup"><span data-stu-id="5c518-163">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="5c518-164">Система внедрения зависимостей создает *один экземпляр* службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-164">DI creates a *single instance* of the service.</span></span>

<span data-ttu-id="5c518-165">Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-165">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="5c518-166">Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-166">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span>

* <span data-ttu-id="5c518-167">Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c518-167">The DI system is based on the DI system in ASP.NET Core.</span></span>
* <span data-ttu-id="5c518-168">Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5c518-168">For more information, see <xref:fundamentals/dependency-injection>.</span></span> <span data-ttu-id="5c518-169">Запрос службы в компоненте</span><span class="sxs-lookup"><span data-stu-id="5c518-169">Request a service in a component</span></span> <span data-ttu-id="5c518-170">После добавления служб в коллекцию служб внедрите их в компоненты, используя директиву [\@inject](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="5c518-170">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>

<span data-ttu-id="5c518-171">[`@inject`](xref:mvc/views/razor#inject) имеет два параметра.</span><span class="sxs-lookup"><span data-stu-id="5c518-171">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

<span data-ttu-id="5c518-172">Тип: тип внедряемой службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-172">Type: The type of the service to inject.</span></span>

<span data-ttu-id="5c518-173">Свойство: имя свойства, получающего внедренную службу приложений.</span><span class="sxs-lookup"><span data-stu-id="5c518-173">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="5c518-174">Свойство не требуется создавать вручную.</span><span class="sxs-lookup"><span data-stu-id="5c518-174">The property doesn't require manual creation.</span></span> <span data-ttu-id="5c518-175">Его создает компилятор.</span><span class="sxs-lookup"><span data-stu-id="5c518-175">The compiler creates the property.</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="5c518-176">Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5c518-176">For more information, see <xref:mvc/views/dependency-injection>.</span></span> <span data-ttu-id="5c518-177">Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб.</span><span class="sxs-lookup"><span data-stu-id="5c518-177">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span> <span data-ttu-id="5c518-178">В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="5c518-178">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="5c518-179">Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-179">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="5c518-180">Обратите внимание, что код использует только абстракцию `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="5c518-180">Note how the code is only using the `IDataAccess` abstraction:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="5c518-181">На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="5c518-181">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span>

<span data-ttu-id="5c518-182">Как правило, этот атрибут не используется напрямую.</span><span class="sxs-lookup"><span data-stu-id="5c518-182">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="5c518-183">Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную:</span><span class="sxs-lookup"><span data-stu-id="5c518-183">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span> <span data-ttu-id="5c518-184">В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется.</span><span class="sxs-lookup"><span data-stu-id="5c518-184">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="5c518-185"><xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно:</span><span class="sxs-lookup"><span data-stu-id="5c518-185">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span> <span data-ttu-id="5c518-186">Использование внедрения зависимостей в службах</span><span class="sxs-lookup"><span data-stu-id="5c518-186">Use DI in services</span></span> <span data-ttu-id="5c518-187">Для сложных служб могут потребоваться дополнительные службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-187">Complex services might require additional services.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="5c518-188">В предыдущем примере для `DataAccess` может потребоваться служба <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5c518-188">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span>

* <span data-ttu-id="5c518-189">[`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах.</span><span class="sxs-lookup"><span data-stu-id="5c518-189">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="5c518-190">Вместо этого следует использовать *внедрения конструктора*.</span><span class="sxs-lookup"><span data-stu-id="5c518-190">*Constructor injection* must be used instead.</span></span>
* <span data-ttu-id="5c518-191">Необходимые службы добавляются путем добавления параметров в конструктор службы.</span><span class="sxs-lookup"><span data-stu-id="5c518-191">Required services are added by adding parameters to the service's constructor.</span></span>
* <span data-ttu-id="5c518-192">Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="5c518-192">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="5c518-193">Необходимые условия для внедрения конструктора:</span><span class="sxs-lookup"><span data-stu-id="5c518-193">Prerequisites for constructor injection:</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="5c518-194">Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5c518-194">One constructor must exist whose arguments can all be fulfilled by DI.</span></span>

<span data-ttu-id="5c518-195">Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5c518-195">Additional parameters not covered by DI are allowed if they specify default values.</span></span> <span data-ttu-id="5c518-196">Применимый конструктор должен быть *общедоступным*.</span><span class="sxs-lookup"><span data-stu-id="5c518-196">The applicable constructor must be *public*.</span></span> <span data-ttu-id="5c518-197">Должен существовать один подходящий конструктор.</span><span class="sxs-lookup"><span data-stu-id="5c518-197">One applicable constructor must exist.</span></span> <span data-ttu-id="5c518-198">В случае неоднозначности система внедрения зависимостей выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="5c518-198">In case of an ambiguity, DI throws an exception.</span></span>

<span data-ttu-id="5c518-199">Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="5c518-199">Utility base component classes to manage a DI scope</span></span> <span data-ttu-id="5c518-200">В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом.</span><span class="sxs-lookup"><span data-stu-id="5c518-200">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="5c518-201">По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="5c518-201">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="5c518-202">В приложениях Blazor Server область запроса существует на протяжении всего клиентского соединения, что может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось.</span><span class="sxs-lookup"><span data-stu-id="5c518-202">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="5c518-203">В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как одноэлементные, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c518-203">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

* <span data-ttu-id="5c518-204">Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="5c518-204">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span>
* <span data-ttu-id="5c518-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span>

<span data-ttu-id="5c518-206">Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент.</span><span class="sxs-lookup"><span data-stu-id="5c518-206">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span>

* <span data-ttu-id="5c518-207">При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-207">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="5c518-208">Это может быть полезно для служб, которые:</span><span class="sxs-lookup"><span data-stu-id="5c518-208">This can be useful for services that:</span></span>

  <span data-ttu-id="5c518-209">требуется повторно использовать в компоненте, так как временное существование не подходит;</span><span class="sxs-lookup"><span data-stu-id="5c518-209">Should be reused within a component, as the transient lifetime is inappropriate.</span></span> <span data-ttu-id="5c518-210">не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит.</span><span class="sxs-lookup"><span data-stu-id="5c518-210">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span> <span data-ttu-id="5c518-211">Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:</span><span class="sxs-lookup"><span data-stu-id="5c518-211">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="5c518-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="5c518-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5c518-213">Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-213">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="5c518-214">Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-214">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="5c518-215">Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="5c518-215">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span>

<span data-ttu-id="5c518-216">Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области.</span><span class="sxs-lookup"><span data-stu-id="5c518-216">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span> <span data-ttu-id="5c518-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="5c518-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="5c518-218">Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента.</span><span class="sxs-lookup"><span data-stu-id="5c518-218">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="5c518-219">Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.</span><span class="sxs-lookup"><span data-stu-id="5c518-219">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

<span data-ttu-id="5c518-220">Использование DbContext Entity Framework из системы внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="5c518-220">Use of Entity Framework DbContext from DI</span></span> <span data-ttu-id="5c518-221">Одним из распространенных типов службы, извлекаемым из системы внедрения зависимостей в веб-приложениях, являются объекты <xref:Microsoft.EntityFrameworkCore.DbContext> Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="5c518-221">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span>

* <span data-ttu-id="5c518-222">Регистрация служб EF с помощью <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> добавляет <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве службы с заданной областью по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5c518-222">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span>
* <span data-ttu-id="5c518-223">Регистрация в качестве службы с заданной областью может привести к проблемам в приложениях Blazor, так как это приводит к длительному существованию экземпляров <xref:Microsoft.EntityFrameworkCore.DbContext> и их совместному использованию в приложении.</span><span class="sxs-lookup"><span data-stu-id="5c518-223">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span>

<span data-ttu-id="5c518-224"><xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не должен использоваться параллельно.</span><span class="sxs-lookup"><span data-stu-id="5c518-224"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span> <span data-ttu-id="5c518-225">В зависимости от приложения использование <xref:Microsoft.AspNetCore.Components.OwningComponentBase> для ограничения области <xref:Microsoft.EntityFrameworkCore.DbContext> одним компонентом *может* решить эту проблему.</span><span class="sxs-lookup"><span data-stu-id="5c518-225">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="5c518-226">Если компонент не использует <xref:Microsoft.EntityFrameworkCore.DbContext> параллельно, достаточно создать производный компонент от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и извлечь <xref:Microsoft.EntityFrameworkCore.DbContext> из <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, так как это гарантирует следующее:</span><span class="sxs-lookup"><span data-stu-id="5c518-226">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="5c518-227">Отдельные компоненты не используют <xref:Microsoft.EntityFrameworkCore.DbContext> совместно.</span><span class="sxs-lookup"><span data-stu-id="5c518-227">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="5c518-228"><xref:Microsoft.EntityFrameworkCore.DbContext> существует столько же, сколько и зависящий от него компонент.</span><span class="sxs-lookup"><span data-stu-id="5c518-228">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>
  * <span data-ttu-id="5c518-229">Если отдельный компонент может использовать <xref:Microsoft.EntityFrameworkCore.DbContext> параллельно (например, каждый раз, когда пользователь нажимает кнопку), даже использование <xref:Microsoft.AspNetCore.Components.OwningComponentBase> не позволяет избежать проблем с параллельными операциями EF.</span><span class="sxs-lookup"><span data-stu-id="5c518-229">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="5c518-230">В этом случае используйте разные <xref:Microsoft.EntityFrameworkCore.DbContext> для каждой логической операции EF.</span><span class="sxs-lookup"><span data-stu-id="5c518-230">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="5c518-231">Вы можете использовать один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="5c518-231">Use either of the following approaches:</span></span> <span data-ttu-id="5c518-232">Создайте <xref:Microsoft.EntityFrameworkCore.DbContext> напрямую с использованием <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> в качестве аргумента, который можно извлечь из системы внедрения зависимостей и является потокобезопасным.</span><span class="sxs-lookup"><span data-stu-id="5c518-232">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="5c518-233">Зарегистрируйте <xref:Microsoft.EntityFrameworkCore.DbContext> в контейнере службы с временным существованием:</span><span class="sxs-lookup"><span data-stu-id="5c518-233">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span> <span data-ttu-id="5c518-234">При регистрации контекста используйте <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="5c518-234">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="5c518-235">Метод расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> принимает два необязательных параметра типа <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span><span class="sxs-lookup"><span data-stu-id="5c518-235">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span>

* <xref:fundamentals/dependency-injection>
* <span data-ttu-id="5c518-236">Чтобы использовать этот подход, только параметр `contextLifetime` должен иметь значение <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="5c518-236">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>
* <xref:mvc/views/dependency-injection>
