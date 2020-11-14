---
title: 'Внедрение зависимостей Blazor в ASP.NET Core'
author: guardrex
description: 'Подробные сведения о том, как приложения Blazor могут внедрять службы в компоненты.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 0cec9a1ea6f6df52103ab190c85518ddc42a573f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507932"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="6cba2-103">Внедрение зависимостей Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6cba2-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="6cba2-104">Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)</span><span class="sxs-lookup"><span data-stu-id="6cba2-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="6cba2-105">Blazor поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6cba2-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6cba2-106">Приложения могут использовать встроенные службы, внедряя их в компоненты.</span><span class="sxs-lookup"><span data-stu-id="6cba2-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="6cba2-107">Приложения также могут определять и регистрировать пользовательские службы и предоставлять к ним доступ в рамках всего приложения с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="6cba2-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="6cba2-108">Внедрение зависимостей — это методика доступа к службам, настроенным в центральном расположении.</span><span class="sxs-lookup"><span data-stu-id="6cba2-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="6cba2-109">Она используется в приложениях Blazor для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="6cba2-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="6cba2-110">Совместное использование одного экземпляра класса службы во множестве компонентов, называемого *одноэлементной* службой.</span><span class="sxs-lookup"><span data-stu-id="6cba2-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="6cba2-111">Отделение компонентов от конкретных классов служб с помощью абстракций ссылок.</span><span class="sxs-lookup"><span data-stu-id="6cba2-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="6cba2-112">Например, рассмотрим интерфейс `IDataAccess` для доступа к данным в приложении.</span><span class="sxs-lookup"><span data-stu-id="6cba2-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="6cba2-113">Этот интерфейс реализуется конкретным классом `DataAccess` и регистрируется в качестве службы в контейнере службы приложения.</span><span class="sxs-lookup"><span data-stu-id="6cba2-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="6cba2-114">Когда компонент использует внедрение зависимостей для получения реализации `IDataAccess`, этот компонент не связывается с конкретным типом.</span><span class="sxs-lookup"><span data-stu-id="6cba2-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="6cba2-115">Реализация может быть переключена, например, для реализации макета в модульных тестах.</span><span class="sxs-lookup"><span data-stu-id="6cba2-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="6cba2-116">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="6cba2-116">Default services</span></span>

<span data-ttu-id="6cba2-117">Службы по умолчанию автоматически добавляются в коллекцию служб приложения.</span><span class="sxs-lookup"><span data-stu-id="6cba2-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="6cba2-118">Служба</span><span class="sxs-lookup"><span data-stu-id="6cba2-118">Service</span></span> | <span data-ttu-id="6cba2-119">Время существования</span><span class="sxs-lookup"><span data-stu-id="6cba2-119">Lifetime</span></span> | <span data-ttu-id="6cba2-120">Описание</span><span class="sxs-lookup"><span data-stu-id="6cba2-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="6cba2-121">Область действия</span><span class="sxs-lookup"><span data-stu-id="6cba2-121">Scoped</span></span> | <span data-ttu-id="6cba2-122">Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.</span><span class="sxs-lookup"><span data-stu-id="6cba2-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="6cba2-123">Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме.</span><span class="sxs-lookup"><span data-stu-id="6cba2-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="6cba2-124">Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, настроенный в качестве службы по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6cba2-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="6cba2-125">Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6cba2-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="6cba2-126">Для получения дополнительной информации см. <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="6cba2-127"><xref:System.Net.Http.HttpClient> регистрируется как служба с заданной областью, а не как singleton.</span><span class="sxs-lookup"><span data-stu-id="6cba2-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="6cba2-128">Дополнительные сведения см. в разделе [Время существования службы](#service-lifetime).</span><span class="sxs-lookup"><span data-stu-id="6cba2-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="6cba2-129">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="6cba2-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6cba2-130">С заданной областью (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="6cba2-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6cba2-131">Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6cba2-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="6cba2-132">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="6cba2-133">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="6cba2-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6cba2-134">С заданной областью (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="6cba2-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6cba2-135">Содержит вспомогательные методы для работы с URI и состоянием навигации.</span><span class="sxs-lookup"><span data-stu-id="6cba2-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="6cba2-136">Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="6cba2-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="6cba2-137">Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически.</span><span class="sxs-lookup"><span data-stu-id="6cba2-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="6cba2-138">Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг.</span><span class="sxs-lookup"><span data-stu-id="6cba2-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="6cba2-139">Добавление служб в приложение</span><span class="sxs-lookup"><span data-stu-id="6cba2-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="6cba2-140">Настройте службы для коллекции служб приложения в методе `Main` файла `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="6cba2-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="6cba2-141">В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="6cba2-142">После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов.</span><span class="sxs-lookup"><span data-stu-id="6cba2-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="6cba2-143">Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого:</span><span class="sxs-lookup"><span data-stu-id="6cba2-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="6cba2-144">Узел также предоставляет центральный экземпляр конфигурации для приложения.</span><span class="sxs-lookup"><span data-stu-id="6cba2-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="6cba2-145">Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, `appsettings.json`) в `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="6cba2-146">После создания приложения изучите метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="6cba2-147">В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов дескриптора службы (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="6cba2-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="6cba2-148">Службы добавляются в метод `ConfigureServices` путем предоставления дескрипторов служб в коллекцию служб.</span><span class="sxs-lookup"><span data-stu-id="6cba2-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="6cba2-149">В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`.</span><span class="sxs-lookup"><span data-stu-id="6cba2-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="6cba2-150">Время существования службы</span><span class="sxs-lookup"><span data-stu-id="6cba2-150">Service lifetime</span></span>

<span data-ttu-id="6cba2-151">Для служб можно настроить параметры времени существования, указанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="6cba2-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="6cba2-152">Время существования</span><span class="sxs-lookup"><span data-stu-id="6cba2-152">Lifetime</span></span> | <span data-ttu-id="6cba2-153">Описание</span><span class="sxs-lookup"><span data-stu-id="6cba2-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="6cba2-154">Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует.</span><span class="sxs-lookup"><span data-stu-id="6cba2-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="6cba2-155">Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="6cba2-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="6cba2-156">Но модель размещения Blazor Server поддерживает время существования `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="6cba2-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="6cba2-157">В приложениях Blazor Server регистрация службы с заданной областью ограничивается *подключением*.</span><span class="sxs-lookup"><span data-stu-id="6cba2-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="6cba2-158">По этой причине использование служб с заданной областью предпочтительно для служб, которые должны быть ограничены текущим пользователем, даже если текущим намерением является запуск на стороне клиента в браузере в приложении Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="6cba2-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser in a Blazor WebAssembly app.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="6cba2-159">Система внедрения зависимостей создает *один экземпляр* службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="6cba2-160">Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="6cba2-161">Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="6cba2-162">Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cba2-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="6cba2-163">Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="6cba2-164">Запрос службы в компоненте</span><span class="sxs-lookup"><span data-stu-id="6cba2-164">Request a service in a component</span></span>

<span data-ttu-id="6cba2-165">После добавления служб в коллекцию служб внедрите их в компоненты, используя директиву [\@inject](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="6cba2-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="6cba2-166">[`@inject`](xref:mvc/views/razor#inject) имеет два параметра.</span><span class="sxs-lookup"><span data-stu-id="6cba2-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="6cba2-167">Тип: тип внедряемой службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="6cba2-168">Свойство: имя свойства, получающего внедренную службу приложений.</span><span class="sxs-lookup"><span data-stu-id="6cba2-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="6cba2-169">Свойство не требуется создавать вручную.</span><span class="sxs-lookup"><span data-stu-id="6cba2-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="6cba2-170">Его создает компилятор.</span><span class="sxs-lookup"><span data-stu-id="6cba2-170">The compiler creates the property.</span></span>

<span data-ttu-id="6cba2-171">Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="6cba2-172">Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб.</span><span class="sxs-lookup"><span data-stu-id="6cba2-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="6cba2-173">В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="6cba2-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="6cba2-174">Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="6cba2-175">Обратите внимание, что код использует только абстракцию `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="6cba2-176">На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="6cba2-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="6cba2-177">Как правило, этот атрибут не используется напрямую.</span><span class="sxs-lookup"><span data-stu-id="6cba2-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="6cba2-178">Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную:</span><span class="sxs-lookup"><span data-stu-id="6cba2-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="6cba2-179">В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется.</span><span class="sxs-lookup"><span data-stu-id="6cba2-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="6cba2-180"><xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно:</span><span class="sxs-lookup"><span data-stu-id="6cba2-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="6cba2-181">Использование внедрения зависимостей в службах</span><span class="sxs-lookup"><span data-stu-id="6cba2-181">Use DI in services</span></span>

<span data-ttu-id="6cba2-182">Для сложных служб могут потребоваться дополнительные службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-182">Complex services might require additional services.</span></span> <span data-ttu-id="6cba2-183">В предыдущем примере для `DataAccess` может потребоваться служба <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6cba2-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="6cba2-184">[`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах.</span><span class="sxs-lookup"><span data-stu-id="6cba2-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="6cba2-185">Вместо этого следует использовать *внедрения конструктора*.</span><span class="sxs-lookup"><span data-stu-id="6cba2-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="6cba2-186">Необходимые службы добавляются путем добавления параметров в конструктор службы.</span><span class="sxs-lookup"><span data-stu-id="6cba2-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="6cba2-187">Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="6cba2-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="6cba2-188">В следующем примере конструктор получает <xref:System.Net.Http.HttpClient> через внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="6cba2-188">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="6cba2-189"><xref:System.Net.Http.HttpClient> — это служба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6cba2-189"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="6cba2-190">Необходимые условия для внедрения конструктора:</span><span class="sxs-lookup"><span data-stu-id="6cba2-190">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="6cba2-191">Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="6cba2-191">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="6cba2-192">Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6cba2-192">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="6cba2-193">Применимый конструктор должен быть `public`.</span><span class="sxs-lookup"><span data-stu-id="6cba2-193">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="6cba2-194">Должен существовать один подходящий конструктор.</span><span class="sxs-lookup"><span data-stu-id="6cba2-194">One applicable constructor must exist.</span></span> <span data-ttu-id="6cba2-195">В случае неоднозначности система внедрения зависимостей выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="6cba2-195">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="6cba2-196">Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="6cba2-196">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="6cba2-197">В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом.</span><span class="sxs-lookup"><span data-stu-id="6cba2-197">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="6cba2-198">По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="6cba2-198">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="6cba2-199">В приложениях Blazor Server область запроса существует на протяжении всего клиентского подключения. Это может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось.</span><span class="sxs-lookup"><span data-stu-id="6cba2-199">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="6cba2-200">В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как singleton, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6cba2-200">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="6cba2-201">См. раздел [Обнаружение высвобождаемых временных служб](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="6cba2-201">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="6cba2-202">Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-202">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="6cba2-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="6cba2-204">Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент.</span><span class="sxs-lookup"><span data-stu-id="6cba2-204">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="6cba2-205">При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-205">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="6cba2-206">Это может быть полезно для служб, которые:</span><span class="sxs-lookup"><span data-stu-id="6cba2-206">This can be useful for services that:</span></span>

* <span data-ttu-id="6cba2-207">требуется повторно использовать в компоненте, так как временное существование не подходит;</span><span class="sxs-lookup"><span data-stu-id="6cba2-207">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="6cba2-208">не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит.</span><span class="sxs-lookup"><span data-stu-id="6cba2-208">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="6cba2-209">Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:</span><span class="sxs-lookup"><span data-stu-id="6cba2-209">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="6cba2-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="6cba2-211">Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-211">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="6cba2-212">Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-212">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="6cba2-213">Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-213">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="6cba2-214">Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области.</span><span class="sxs-lookup"><span data-stu-id="6cba2-214">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="6cba2-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="6cba2-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="6cba2-216">Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента.</span><span class="sxs-lookup"><span data-stu-id="6cba2-216">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="6cba2-217">Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.</span><span class="sxs-lookup"><span data-stu-id="6cba2-217">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="6cba2-218">Использование DbContext Entity Framework Core (EF Core) из внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="6cba2-218">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="6cba2-219">Дополнительные сведения см. в разделе <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-219">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="6cba2-220">Обнаружение высвобождаемых временных служб</span><span class="sxs-lookup"><span data-stu-id="6cba2-220">Detect transient disposables</span></span>

<span data-ttu-id="6cba2-221">В следующих примерах показано, как обнаружить высвобождаемые временные службы в приложении, которые должны использовать <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="6cba2-221">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="6cba2-222">См. раздел [Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="6cba2-222">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="6cba2-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="6cba2-224">В следующем примере обнаруживается `TransientDisposable` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6cba2-224">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="6cba2-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="6cba2-226">`Program`:</span><span class="sxs-lookup"><span data-stu-id="6cba2-226">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="6cba2-227">В следующем примере обнаруживается `TransientDependency` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="6cba2-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="6cba2-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6cba2-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="6cba2-229">Руководство по применению временных и общих экземпляров `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="6cba2-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
