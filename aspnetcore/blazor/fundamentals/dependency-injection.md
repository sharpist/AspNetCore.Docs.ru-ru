---
title: Внедрение зависимостей Blazor в ASP.NET Core
author: guardrex
description: Подробные сведения о том, как приложения Blazor могут внедрять службы в компоненты.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 3dc15f5efcc8f48a809bf9132588fb38732a7b35
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628291"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>Внедрение зависимостей Blazor в ASP.NET Core

Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)

Blazor поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). Приложения могут использовать встроенные службы, внедряя их в компоненты. Приложения также могут определять и регистрировать пользовательские службы и предоставлять к ним доступ в рамках всего приложения с помощью внедрения зависимостей.

Внедрение зависимостей — это методика доступа к службам, настроенным в центральном расположении. Она используется в приложениях Blazor для выполнения следующих задач:

* Совместное использование одного экземпляра класса службы во множестве компонентов, называемого *одноэлементной* службой.
* Отделение компонентов от конкретных классов служб с помощью абстракций ссылок. Например, рассмотрим интерфейс `IDataAccess` для доступа к данным в приложении. Этот интерфейс реализуется конкретным классом `DataAccess` и регистрируется в качестве службы в контейнере службы приложения. Когда компонент использует внедрение зависимостей для получения реализации `IDataAccess`, этот компонент не связывается с конкретным типом. Реализация может быть переключена, например, для реализации макета в модульных тестах.

## <a name="default-services"></a>Службы по умолчанию

Службы по умолчанию автоматически добавляются в коллекцию служб приложения.

| Служба | Время существования | Описание |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Область действия | Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.<br><br>Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме.<br><br>Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, настроенный в качестве службы по умолчанию. Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server.<br><br>Для получения дополнительной информации см. <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)<br>С заданной областью (Blazor Server) | Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript. Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)<br>С заданной областью (Blazor Server) | Содержит вспомогательные методы для работы с URI и состоянием навигации. Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически. Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг.

## <a name="add-services-to-an-app"></a>Добавление служб в приложение

### Blazor WebAssembly

Настройте службы для коллекции служб приложения в методе `Main` файла `Program.cs`. В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:

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
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов. Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Узел также предоставляет центральный экземпляр конфигурации для приложения. Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, `appsettings.json`) в `InitializeWeatherAsync`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

После создания приложения изучите метод `Startup.ConfigureServices`:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов дескриптора службы (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Службы добавляются в метод `ConfigureServices` путем предоставления дескрипторов служб в коллекцию служб. В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Время существования службы

Для служб можно настроить параметры времени существования, указанные в следующей таблице.

| Время существования | Описание |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует. Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`. Но модель размещения Blazor Server поддерживает время существования `Scoped`. В приложениях Blazor Server регистрация службы с заданной областью ограничивается *подключением*. По этой причине использование служб с заданной областью предпочтительно для служб, которые должны быть ограничены текущим пользователем, даже если текущим намерением является запуск на стороне клиента в браузере. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Система внедрения зависимостей создает *один экземпляр* службы. Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы. |

Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core. Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Запрос службы в компоненте

После добавления служб в коллекцию служб внедрите их в компоненты, используя директиву [\@inject](xref:mvc/views/razor#inject) Razor. [`@inject`](xref:mvc/views/razor#inject) имеет два параметра.

* Тип: тип внедряемой службы.
* Свойство: имя свойства, получающего внедренную службу приложений. Свойство не требуется создавать вручную. Его создает компилятор.

Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>.

Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб.

В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject). Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента. Обратите внимание, что код использует только абстракцию `IDataAccess`:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute). Как правило, этот атрибут не используется напрямую. Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется. <xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Использование внедрения зависимостей в службах

Для сложных служб могут потребоваться дополнительные службы. В предыдущем примере для `DataAccess` может потребоваться служба <xref:System.Net.Http.HttpClient> по умолчанию. [`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах. Вместо этого следует использовать *внедрения конструктора*. Необходимые службы добавляются путем добавления параметров в конструктор службы. Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом. В следующем примере конструктор получает <xref:System.Net.Http.HttpClient> через внедрение зависимостей. <xref:System.Net.Http.HttpClient> — это служба по умолчанию.

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Необходимые условия для внедрения конструктора:

* Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей. Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию.
* Применимый конструктор должен быть `public`.
* Должен существовать один подходящий конструктор. В случае неоднозначности система внедрения зависимостей выдает исключение.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей

В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом. По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей. В приложениях Blazor Server область запроса существует на протяжении всего клиентского подключения. Это может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось. В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как singleton, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.

> [!NOTE]
> См. раздел [Обнаружение высвобождаемых временных служб](#detect-transient-disposables).

Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента. Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент. При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента. Это может быть полезно для служб, которые:

* требуется повторно использовать в компоненте, так как временное существование не подходит;
* не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит.

Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>. Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента.

  Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента. Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>. Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области.

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью. Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента. Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Использование DbContext Entity Framework Core (EF Core) из внедрения зависимостей

Дополнительные сведения см. в разделе <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Обнаружение высвобождаемых временных служб

В следующих примерах показано, как обнаружить высвобождаемые временные службы в приложении, которые должны использовать <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. См. раздел [Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей](#utility-base-component-classes-to-manage-a-di-scope).

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

В следующем примере обнаруживается `TransientDisposable` (`Program.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

В следующем примере обнаруживается `TransientDependency` (`Startup.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/dependency-injection>
* [Руководство по применению временных и общих экземпляров `IDisposable`](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
