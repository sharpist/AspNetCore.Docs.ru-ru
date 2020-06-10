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
# <a name="aspnet-core-blazor-dependency-injection"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: blazor/dependency-injection

Внедрение зависимостей Blazor в ASP.NET Core Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Майк Роусос](https://github.com/mjrousos) (Mike Rousos)

* Blazor поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).
* Приложения могут использовать встроенные службы, внедряя их в компоненты. Приложения также могут определять и регистрировать пользовательские службы и предоставлять к ним доступ в рамках всего приложения с помощью внедрения зависимостей. Внедрение зависимостей — это методика доступа к службам, настроенным в центральном расположении. Она используется в приложениях Blazor для выполнения следующих задач: Совместное использование одного экземпляра класса службы во множестве компонентов, называемого *одноэлементной* службой.

## <a name="default-services"></a>Отделение компонентов от конкретных классов служб с помощью абстракций ссылок.

Например, рассмотрим интерфейс `IDataAccess` для доступа к данным в приложении.

| Этот интерфейс реализуется конкретным классом `DataAccess` и регистрируется в качестве службы в контейнере службы приложения. | Когда компонент использует внедрение зависимостей для получения реализации `IDataAccess`, этот компонент не связывается с конкретным типом. | Реализация может быть переключена, например, для реализации макета в модульных тестах. |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Службы по умолчанию | Службы по умолчанию автоматически добавляются в коллекцию служб приложения.<br><br>Служба<br><br>Время существования Описание<br><br>Временный |
| <xref:Microsoft.JSInterop.IJSRuntime> | Предоставляет методы для отправки HTTP-запросов и получения HTTP-ответов от ресурса с заданным URI.<br>Экземпляр <xref:System.Net.Http.HttpClient> в приложении Blazor WebAssembly использует браузер для обработки HTTP-трафика в фоновом режиме. | Приложения Blazor Server не включают клиент <xref:System.Net.Http.HttpClient>, по умолчанию настроенный в качестве службы. Предоставьте <xref:System.Net.Http.HttpClient> приложению Blazor Server. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Для получения дополнительной информации см. <xref:blazor/call-web-api>.<br>Отдельная (Blazor WebAssembly) | С областью действия (Blazor Server) Представляет экземпляр среды выполнения JavaScript, в которую отправляются вызовы JavaScript. |

Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>. Отдельная (Blazor WebAssembly)

## <a name="add-services-to-an-app"></a>С областью действия (Blazor Server)

### <a name="blazor-webassembly"></a>Содержит вспомогательные методы для работы с URI и состоянием навигации.

Дополнительные сведения см. в разделе [URI и вспомогательные инструменты состояния навигации](xref:blazor/routing#uri-and-navigation-state-helpers). Пользовательский поставщик услуг не предоставляет перечисленные в таблице службы по умолчанию автоматически.

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

Если вы используете пользовательский поставщик услуг и нуждаетесь в какой-либо из служб, указанных в таблице, добавьте необходимые службы в новый поставщик услуг. Добавление служб в приложение

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

Blazor WebAssembly Настройте службы для коллекции служб приложения в методе `Main` файла *Program.cs*.

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

### <a name="blazor-server"></a>В следующем примере реализация `MyDependency` зарегистрирована для `IMyDependency`:

После сборки узла доступ к службам можно получить из корневой области внедрения зависимостей до отрисовки каких-либо компонентов.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Это может быть удобно для запуска логики инициализации перед отрисовкой содержимого: Узел также предоставляет центральный экземпляр конфигурации для приложения. Основываясь на предыдущем примере, URL-адрес службы погоды передается из источника конфигурации по умолчанию (например, *appsettings.json*) в `InitializeWeatherAsync`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Сервер Blazor

После создания приложения изучите метод `Startup.ConfigureServices`:

| В метод <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> передается <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, представляющий собой список объектов дескриптора службы (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). | Службы добавляются путем предоставления дескрипторов служб в коллекцию служб. |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | В следующем примере показана концепция с интерфейсом `IDataAccess` и его конкретной реализацией `DataAccess`. Время существования службы Для служб можно настроить параметры времени существования, указанные в следующей таблице. Время существования Описание |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Сейчас в приложениях Blazor WebAssembly концепция областей внедрения зависимостей отсутствует. Службы, зарегистрированные как `Scoped`, работают аналогично службам `Singleton`. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Однако модель размещения сервера Blazor поддерживает время существования `Scoped`. |

В приложениях Blazor Server регистрация службы с заданной областью ограничивается *соединением*. По этой причине использование служб с заданной областью предпочтительно для служб, которые должны быть ограничены текущим пользователем, даже если текущим намерением является запуск на стороне клиента в браузере.

## <a name="request-a-service-in-a-component"></a>Система внедрения зависимостей создает *один экземпляр* службы.

Все компоненты, для которых необходима служба `Singleton`, получают экземпляр той же службы. Каждый раз, когда компонент получает экземпляр службы `Transient` из контейнера службы, он получает *новый экземпляр* этой службы.

* Система внедрения зависимостей основана на системе внедрения зависимостей в ASP.NET Core.
* Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>. Запрос службы в компоненте После добавления служб в коллекцию служб внедрите их в компоненты, используя директиву [\@inject](xref:mvc/views/razor#inject) Razor.

[`@inject`](xref:mvc/views/razor#inject) имеет два параметра.

Тип: тип внедряемой службы.

Свойство: имя свойства, получающего внедренную службу приложений. Свойство не требуется создавать вручную. Его создает компилятор.

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Для получения дополнительной информации см. <xref:mvc/views/dependency-injection>. Используйте несколько инструкций [`@inject`](xref:mvc/views/razor#inject) для внедрения различных служб. В следующем примере показано, как использовать [`@inject`](xref:mvc/views/razor#inject).

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Служба, реализующая `Services.IDataAccess`, внедряется в свойство `DataRepository` компонента. Обратите внимание, что код использует только абстракцию `IDataAccess`:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>На внутреннем уровне создаваемое свойство (`DataRepository`) использует атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).

Как правило, этот атрибут не используется напрямую. Если базовый класс необходим для компонентов, а для базового класса также требуются обязательные свойства, добавьте атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) вручную: В компонентах, производных от базового класса, директива [`@inject`](xref:mvc/views/razor#inject) не требуется. <xref:Microsoft.AspNetCore.Components.InjectAttribute> базового класса достаточно: Использование внедрения зависимостей в службах Для сложных служб могут потребоваться дополнительные службы.

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

В предыдущем примере для `DataAccess` может потребоваться служба <xref:System.Net.Http.HttpClient> по умолчанию.

* [`@inject`](xref:mvc/views/razor#inject) (или атрибут [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) невозможно использовать в службах. Вместо этого следует использовать *внедрения конструктора*.
* Необходимые службы добавляются путем добавления параметров в конструктор службы.
* Когда система внедрения зависимостей создает службу, она распознает необходимые службы в конструкторе и предоставляет их соответствующим образом. Необходимые условия для внедрения конструктора:

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Должен существовать один конструктор, аргументы которого могут использоваться системой внедрения зависимостей.

Дополнительные параметры, не охваченные системой внедрения зависимостей, разрешены, если они указывают значения по умолчанию. Применимый конструктор должен быть *общедоступным*. Должен существовать один подходящий конструктор. В случае неоднозначности система внедрения зависимостей выдает исключение.

Служебные классы базовых компонентов служебной программы для управления областью внедрения зависимостей В приложениях ASP.NET Core службы с заданной областью обычно ограничены текущим запросом. По завершении запроса все временные службы или службы с заданной областью удаляются системой внедрения зависимостей. В приложениях Blazor Server область запроса существует на протяжении всего клиентского соединения, что может привести к тому, что временные службы или службы с заданной областью будут работать намного дольше, чем ожидалось. В приложениях Blazor WebAssembly службы, зарегистрированные с ограниченным временем существования, рассматриваются как одноэлементные, поэтому они существуют дольше, чем службы с заданной областью в типичных приложениях ASP.NET Core.

* Подход, ограничивающий время существования службы в приложениях Blazor, заключается в использовании типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.
* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактным типом, производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>, который создает область внедрения зависимостей, соответствующую времени существования компонента.

Используя эту область, можно использовать службы внедрения зависимостей с ограниченным временем существования, заставляя их существовать так же долго, как и компонент.

* При удалении компонента также удаляются и службы из поставщика служб с заданной областью действия этого компонента. Это может быть полезно для служб, которые:

  требуется повторно использовать в компоненте, так как временное существование не подходит; не должны совместно использоваться компонентами, так как одноэлементное время существования не подходит. Доступны две версии типа <xref:Microsoft.AspNetCore.Components.OwningComponentBase>:

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> является абстрактной высвобождаемой дочерней версией типа <xref:Microsoft.AspNetCore.Components.ComponentBase> с защищенным свойством <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> типа <xref:System.IServiceProvider>. Этот поставщик можно использовать для разрешения служб, ограниченных временем существования компонента. Службы внедрения зависимостей, внедренные в компонент с помощью атрибута [`@inject`](xref:mvc/views/razor#inject) или [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute), не создаются в области компонента.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Чтобы использовать область компонента, необходимо разрешить службы с помощью <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> или <xref:System.IServiceProvider.GetService%2A>.

Все службы, разрешенные с помощью поставщика <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, имеют свои зависимости из этой же области. <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> является производным от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и добавляет свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A>, которое возвращает экземпляр `T` из поставщика внедрения зависимостей с заданной областью. Этот тип удобен для доступа к службам с заданной областью без использования экземпляра <xref:System.IServiceProvider> при наличии одной основной службы, которую приложение запрашивает из контейнера внедрения зависимостей с использованием области компонента. Свойство <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> доступно, поэтому при необходимости приложение может получить службы других типов.

Использование DbContext Entity Framework из системы внедрения зависимостей Одним из распространенных типов службы, извлекаемым из системы внедрения зависимостей в веб-приложениях, являются объекты <xref:Microsoft.EntityFrameworkCore.DbContext> Entity Framework (EF).

* Регистрация служб EF с помощью <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> добавляет <xref:Microsoft.EntityFrameworkCore.DbContext> в качестве службы с заданной областью по умолчанию.
* Регистрация в качестве службы с заданной областью может привести к проблемам в приложениях Blazor, так как это приводит к длительному существованию экземпляров <xref:Microsoft.EntityFrameworkCore.DbContext> и их совместному использованию в приложении.

<xref:Microsoft.EntityFrameworkCore.DbContext> не является потокобезопасным и не должен использоваться параллельно. В зависимости от приложения использование <xref:Microsoft.AspNetCore.Components.OwningComponentBase> для ограничения области <xref:Microsoft.EntityFrameworkCore.DbContext> одним компонентом *может* решить эту проблему. Если компонент не использует <xref:Microsoft.EntityFrameworkCore.DbContext> параллельно, достаточно создать производный компонент от <xref:Microsoft.AspNetCore.Components.OwningComponentBase> и извлечь <xref:Microsoft.EntityFrameworkCore.DbContext> из <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>, так как это гарантирует следующее:

* Отдельные компоненты не используют <xref:Microsoft.EntityFrameworkCore.DbContext> совместно.

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

* <xref:Microsoft.EntityFrameworkCore.DbContext> существует столько же, сколько и зависящий от него компонент.
  * Если отдельный компонент может использовать <xref:Microsoft.EntityFrameworkCore.DbContext> параллельно (например, каждый раз, когда пользователь нажимает кнопку), даже использование <xref:Microsoft.AspNetCore.Components.OwningComponentBase> не позволяет избежать проблем с параллельными операциями EF. В этом случае используйте разные <xref:Microsoft.EntityFrameworkCore.DbContext> для каждой логической операции EF. Вы можете использовать один из следующих подходов: Создайте <xref:Microsoft.EntityFrameworkCore.DbContext> напрямую с использованием <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> в качестве аргумента, который можно извлечь из системы внедрения зависимостей и является потокобезопасным.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Зарегистрируйте <xref:Microsoft.EntityFrameworkCore.DbContext> в контейнере службы с временным существованием: При регистрации контекста используйте <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.

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

## <a name="additional-resources"></a>Метод расширения <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> принимает два необязательных параметра типа <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.

* <xref:fundamentals/dependency-injection>
* Чтобы использовать этот подход, только параметр `contextLifetime` должен иметь значение <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.
* <xref:mvc/views/dependency-injection>
