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
# <a name="dependency-injection-in-aspnet-core"></a>'Blazor'

'Identity'

::: moniker range=">= aspnetcore-3.0"

'Let's Encrypt'

'Razor'

'SignalR' uid: fundamentals/dependency-injection

## <a name="overview-of-dependency-injection"></a>Внедрение зависимостей в ASP.NET Core

Авторы: [Стив Смит](https://ardalis.com/) (Steve Smith), [Скотт Эдди](https://scottaddie.com) (Scott Addie) и [Брэндон Далер](https://github.com/brandondahler) (Brandon Dahler) ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

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

Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>. [Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))

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

Общие сведения о внедрении зависимостей *Зависимость* — это любой объект, который требуется другому объекту.

* Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.
* Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`. Класс `MyDependency` выступает зависимостью класса `IndexModel`.
* Этот класс создает экземпляр `MyDependency` и напрямую зависит от него. Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:

Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.

* Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.
* В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению. Такая реализация плохо подходит для модульных тестов. В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.
* Внедрение зависимостей устраняет эти проблемы следующим образом: Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.

Зависимость регистрируется в контейнере служб.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Службы регистрируются в приложении в методе `Startup.ConfigureServices`. Служба *внедряется* в конструктор класса там, где он используется. Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен. В [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению. Этот интерфейс реализуется конкретным типом, `MyDependency`.

`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе. Использование цепочки внедрений зависимостей не является чем-то необычным. Каждая запрашиваемая зависимость запрашивает собственные зависимости.

Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*. `IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб. `IMyDependency` регистрируется в `Startup.ConfigureServices`.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа. Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types). В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`. Регистрация регулирует время существования службы согласно времени существования одного запроса.

Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.

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

Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы. Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.

Рекомендуем придерживаться такого подхода в приложениях.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.

Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Поле используется во всем классе для доступа к службе (по мере необходимости).

## <a name="framework-provided-services"></a>В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.

Службы, внедренные в конструктор Startup При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб: Службы можно внедрить в `Startup.Configure`: Для получения дополнительной информации см. <xref:fundamentals/startup>.

| Платформенные службы | Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC. |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)). |
| `IHostApplicationLifetime` | Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой. |
| `IWebHostEnvironment` | В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб. |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Тип службы |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Время существования |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Временный |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Временный |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Временный |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Одноэлементный |

## <a name="register-additional-services-with-extension-methods"></a>Одноэлементный

Временный Одноэлементный

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

Одноэлементный

## <a name="service-lifetimes"></a>Одноэлементный

Регистрация дополнительных служб с помощью методов расширения Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.

### <a name="transient"></a>Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам. Время существования служб

### <a name="scoped"></a>Для каждой зарегистрированной службы выбирайте подходящее время существования.

Для служб ASP.NET можно настроить следующие параметры времени существования:

> [!WARNING]
> Временный Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб. Это время существования лучше всего подходит для простых служб без отслеживания состояния.

### <a name="singleton"></a>Область действия

Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения). При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`. Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект. Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

> [!WARNING]
> Одноэлементный Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).

## <a name="service-registration-methods"></a>Созданный экземпляр используют все последующие запросы.

Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.

| Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется. | Разрешать регулируемую службу из одиночной нельзя.<br>При обработке последующих запросов это может вызвать неправильное состояние службы.<br>Методы регистрации службы | Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.<br>Метод | Автоматический |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>object<br>`services.AddSingleton<IMyDep, MyDep>();` | удаление | Несколько | реализации |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Передача аргументов<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Пример. | Да | Да |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Нет<br>`services.AddSingleton<MyDep>();` | Примеры | Да | Да |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Да<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Пример. | Да | Нет |
| `AddSingleton(new {IMPLEMENTATION})`<br>Нет<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Примеры | Нет | Да |

Да Примеры

Нет

Нет Да

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services). Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация. В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`. Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:

Дополнительные сведения можно найти в разделе Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*. Несколько служб разрешается через `IEnumerable<{SERVICE}>`.

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.

Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.

* <xref:System.IServiceProvider>
* В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`. Вторая строка регистрирует `MyDep` для `IMyDep2`.

Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:

Поведение внедрения через конструктор

Службы можно разрешать двумя методами: <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.

## <a name="entity-framework-contexts"></a>`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.

Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию. Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор. Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.

## <a name="lifetime-and-registration-options"></a>Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.

Контексты Entity Framework Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных. Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Параметры времени существования и регистрации Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.

* В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы. Интерфейсы реализованы в классе `Operation`. Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.
* Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`. Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.
* Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

`OperationService` получает новый экземпляр класса `IOperationTransient`.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Новый экземпляр возвращает другой идентификатор `OperationId`. Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.

В разных клиентских запросах обе службы используют разные значения `OperationId`. Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб. В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.

Понятно, когда этот тип используется (его GUID — все нули).

В примере приложения показано время существования объектов в пределах одного и нескольких запросов.

В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.  
После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.  
Результаты двух запросов будут выглядеть так:  
**Первый запрос**

Операции контроллера:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64

Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000  
**Второй запрос**

Операции контроллера:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

* Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4 Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9
* Instance: 00000000-0000-0000-0000-000000000000
* Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.

## <a name="call-services-from-main"></a>*Временные* объекты всегда разные.

Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`. Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр. *Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.

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

## <a name="scope-validation"></a>*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.

Вызов служб из функции main

* Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.
* Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.

В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`: Проверка области

Когда приложение выполняется в среде разработки и вызывает [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) для сборки узла, поставщик службы по умолчанию проверяет следующее: Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно. Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.

Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.

## <a name="request-services"></a>Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.

Службы с заданной областью удаляются создавшим их контейнером.

Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера. Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.

Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>. Службы запросов Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

> [!NOTE]
> Службы запросов — это все настроенные и запрашиваемые в приложении службы.

## <a name="design-services-for-dependency-injection"></a>Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.

Как правило, использовать эти свойства в приложении напрямую не следует.

* Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.
* Этот процесс создает классы, которые легче тестировать. Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.
* Проектирование служб для внедрения зависимостей Придерживайтесь следующих рекомендаций:
* Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.

Избегайте статических классов и членов с отслеживанием состояния. Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния. Избегайте прямого создания экземпляров зависимых классов внутри служб. Прямое создание экземпляров обязывает использовать в коде определенную реализацию.

### <a name="disposal-of-services"></a>Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.

Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.

Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.

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

Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

* Удаление служб
* Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.
* Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.
* В следующем примере службы создаются контейнером службы и автоматически удаляются:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>В следующем примере:

#### <a name="transient-limited-lifetime"></a>Экземпляры службы не создаются контейнером службы.

Платформе неизвестно предполагаемое время жизни службы.

Платформа не удаляет службы автоматически.

* Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.
* Руководство по применению временных и общих экземпляров IDisposable

Временный экземпляр, ограниченное время существования

**Сценарий** Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев: Экземпляр разрешается в корневой области.

* Экземпляр должен быть удален до завершения области.
* **Решение**

#### <a name="shared-instance-limited-lifetime"></a>Используйте шаблон фабрики для создания экземпляра за пределами родительской области.

В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.

Если окончательный тип имеет другие зависимости, фабрика позволяет:

Получить <xref:System.IServiceProvider> в своем конструкторе.

Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей. Общий экземпляр, ограниченное время существования **Сценарий** Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.

#### <a name="general-guidelines"></a>**Решение**

* Зарегистрируйте экземпляр с временем существования с заданной областью. Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.
* Используйте <xref:System.IServiceProvider> области для получения необходимых служб. Удалить область по истечении времени существования.
* Общие рекомендации Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.
* Вместо этого используйте шаблон фабрики. Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.

## <a name="default-service-container-replacement"></a>Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.

Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>. Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.

* Области должны использоваться для управления жизненным циклом служб.
* Области не являются иерархическими, и между ними нет специальной связи.
* Замена стандартного контейнера служб
* Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.
* Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:
* Внедрение свойств

Внедрение по имени

* Дочерние контейнеры
* Настраиваемое управление временем существования
* `Func<T>` поддерживает отложенную инициализацию
* Регистрация на основе соглашения
* С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:
* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);

### <a name="thread-safety"></a>[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);

[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection); [Lamar](https://jasperfx.github.io/lamar/);

[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection); [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a>Потокобезопасность

* Создавайте потокобезопасные одноэлементные службы. Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.

* Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным. Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком. Рекомендации Разрешение служб на основе `async/await` и `Task` не поддерживается. C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.

* Не храните данные и конфигурацию непосредственно в контейнере служб.

* Например, обычно не следует добавлять корзину пользователя в контейнер служб. Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).

  Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.

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

  Лучше запросить фактический элемент через внедрение зависимостей.

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

* Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)). Старайтесь не использовать *схему указателя служб*.

* Например, не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей:

**Неправильно**: **Правильно**:

Другой вариант указателя службы, позволяющий избежать этого, — внедрение фабрики, которая разрешает зависимости во время выполнения. Оба метода смешивают стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил. Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.

Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.

## <a name="additional-resources"></a>Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* Рекомендуемые подходы к мультитенантности при внедрении зависимостей
* Мультитенантность поддерживается в [Orchard Core](https://github.com/OrchardCMS/OrchardCore).
* Дополнительные сведения см. в [документации по Orchard Core](https://docs.orchardcore.net/en/dev/).
* Примеры создания модульных и мультитенантных приложений с использованием только Orchard Core Framework без каких-либо особых функций CMS см. по адресу https://github.com/OrchardCMS/OrchardCore.Samples.
* Дополнительные ресурсы

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Четыре способа удаления интерфейсов IDisposable в ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[Написание чистого кода в ASP.NET Core с внедрением зависимостей (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)

[Принцип явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)

## <a name="overview-of-dependency-injection"></a>[Контейнеры с инверсией управления и шаблон внедрения зависимостей (Мартин Фаулер)](https://www.martinfowler.com/articles/injection.html)

[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Регистрация службы с несколькими интерфейсами с помощью внедрения зависимостей ASP.NET Core) ASP.NET Core поддерживает проектирование программного обеспечения с возможностью внедрения зависимостей. При таком подходе достигается [инверсия управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) между классами и их зависимостями.

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

Дополнительные сведения о внедрении зависимостей в контроллерах MVC см. в статье <xref:mvc/controllers/dependency-injection>. [Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([как скачивать](xref:index#how-to-download-a-sample))

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

Общие сведения о внедрении зависимостей *Зависимость* — это любой объект, который требуется другому объекту.

* Рассмотрим класс `MyDependency` с методом `WriteMessage`, от которого зависят другие классы в приложении.
* Чтобы сделать метод `WriteMessage` доступным какому-то классу, можно создать экземпляр класса `MyDependency`. Класс `MyDependency` выступает зависимостью класса `IndexModel`.
* Этот класс создает экземпляр `MyDependency` и напрямую зависит от него. Зависимости в коде (как в предыдущем примере) представляют собой определенную проблему. Их следует избегать по следующим причинам:

Чтобы заменить `MyDependency` другой реализацией, класс необходимо изменить.

* Если у `MyDependency` есть зависимости, их конфигурацию должен выполнять класс.
* В больших проектах, когда от `MyDependency` зависят многие классы, код конфигурации растягивается по всему приложению. Такая реализация плохо подходит для модульных тестов. В приложении нужно использовать имитацию или заглушку в виде класса `MyDependency`, что при таком подходе невозможно.
* Внедрение зависимостей устраняет эти проблемы следующим образом: Используется интерфейс или базовый класс для абстрагирования реализации зависимостей.

Зависимость регистрируется в контейнере служб.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

ASP.NET Core предоставляет встроенный контейнер служб, <xref:System.IServiceProvider>.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Службы регистрируются в приложении в методе `Startup.ConfigureServices`. Служба *внедряется* в конструктор класса там, где он используется. Платформа берет на себя создание экземпляра зависимости и его удаление, когда он больше не нужен. В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) интерфейс `IMyDependency` определяет метод, который служба предоставляет приложению. Этот интерфейс реализуется конкретным типом, `MyDependency`.

`MyDependency` запрашивает <xref:Microsoft.Extensions.Logging.ILogger`1> в своем конструкторе. Использование цепочки внедрений зависимостей не является чем-то необычным. Каждая запрашиваемая зависимость запрашивает собственные зависимости.

Контейнер разрешает зависимости в графе и возвращает полностью разрешенную службу.

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Весь набор зависимостей, которые нужно разрешить, обычно называют *деревом зависимостей*, *графом зависимостей* или *графом объектов*. `IMyDependency` и `ILogger<TCategoryName>` должны быть зарегистрированы в контейнере служб. `IMyDependency` регистрируется в `Startup.ConfigureServices`.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Службу `ILogger<TCategoryName>` регистрирует инфраструктура абстракций ведения журналов, поэтому такая служба является [платформенной](#framework-provided-services), что означает, что ее по умолчанию регистрирует платформа. Контейнер разрешает `ILogger<TCategoryName>`, используя преимущества [(универсальных) открытых типов](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), что устраняет необходимость регистрации каждого [(универсального) сконструированного типа](/dotnet/csharp/language-reference/language-specification/types#constructed-types). В примере приложения служба `IMyDependency` зарегистрирована с конкретным типом `MyDependency`. Регистрация регулирует время существования службы согласно времени существования одного запроса.

Подробнее о [времени существования служб](#service-lifetimes) мы поговорим далее в этой статье.

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

Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет (а потенциально и настраивает) службы. Например, `services.AddMvc()` добавляет службы Razor, которые нужны для Pages и MVC.

Рекомендуем придерживаться такого подхода в приложениях.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.

Если конструктору службы требуется [встроенный тип](/dotnet/csharp/language-reference/keywords/built-in-types-table), например `string`, его можно внедрить с помощью [конфигурации](xref:fundamentals/configuration/index) или [шаблона параметров](xref:fundamentals/configuration/options).

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Экземпляр службы запрашивается через конструктор класса, в котором эта служба используется и назначается скрытому полю.

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Поле используется во всем классе для доступа к службе (по мере необходимости).

## <a name="framework-provided-services"></a>В примере приложения запрашивается экземпляр `IMyDependency`, который затем используется для вызова метода службы `WriteMessage`.

Службы, внедренные в конструктор Startup При использовании универсального узла (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) в конструктор `Startup` могут внедряться только следующие типы служб: Службы можно внедрить в `Startup.Configure`: Для получения дополнительной информации см. <xref:fundamentals/startup>.

| Платформенные службы | Метод `Startup.ConfigureServices` отвечает за определение служб, которые использует приложение, включая такие компоненты, как Entity Framework Core и ASP.NET Core MVC. |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Изначально коллекция `IServiceCollection`, предоставленная для `ConfigureServices`, содержит определенные платформой службы (в зависимости от [настройки узла](xref:fundamentals/index#host)). |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Приложение, основанное на шаблоне ASP.NET Core, часто содержит сотни служб, зарегистрированных платформой. |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | В следующей таблице перечислены некоторые примеры зарегистрированных платформой служб. |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Тип службы |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Время существования |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Временный |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Временный |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Одноэлементный |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Временный |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Одноэлементный |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Одноэлементный |

## <a name="register-additional-services-with-extension-methods"></a>Одноэлементный

Временный Одноэлементный

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

Одноэлементный

## <a name="service-lifetimes"></a>Одноэлементный

Регистрация дополнительных служб с помощью методов расширения Если зарегистрировать службу (включая ее зависимые службы, если нужно) можно, используя метод расширения коллекции служб, для регистрации всех служб, необходимых этой службе, рекомендуется использовать один метод расширения `Add{SERVICE_NAME}`.

### <a name="transient"></a>Ниже приведен пример того, как добавить дополнительные службы в контейнер с помощью методов расширения [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) и <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

Дополнительные сведения см. в разделе о классе <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> в документации по API-интерфейсам. Время существования служб

### <a name="scoped"></a>Для каждой зарегистрированной службы выбирайте подходящее время существования.

Для служб ASP.NET можно настроить следующие параметры времени существования:

> [!WARNING]
> Временный Временные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) создаются при каждом их запросе из контейнера служб. Это время существования лучше всего подходит для простых служб без отслеживания состояния.

### <a name="singleton"></a>Область действия

Службы времени существования с заданной областью (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) создаются один раз для каждого клиентского запроса (подключения). При использовании такой службы в ПО промежуточного слоя внедрите ее в метод `Invoke` или `InvokeAsync`. Не внедряйте службу с помощью [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection), поскольку в таком случае служба будет вести себя как одноэлементный объект. Для получения дополнительной информации см. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

> [!WARNING]
> Одноэлементный Отдельные службы времени существования (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) создаются при первом запросе (или при выполнении `Startup.ConfigureServices`, когда экземпляр указан во время регистрации службы).

## <a name="service-registration-methods"></a>Созданный экземпляр используют все последующие запросы.

Если в приложении нужно использовать одинарные службы, рекомендуется разрешить контейнеру служб управлять временем их существования.

| Реализовывать конструктивный шаблон с одинарными службами и предоставлять пользовательский код для управления временем существования объекта в классе категорически не рекомендуется. | Разрешать регулируемую службу из одиночной нельзя.<br>При обработке последующих запросов это может вызвать неправильное состояние службы.<br>Методы регистрации службы | Методы расширения регистрации службы предлагают перегрузки, которые полезны в определенных сценариях.<br>Метод | Автоматический |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>object<br>`services.AddSingleton<IMyDep, MyDep>();` | удаление | Несколько | реализации |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Передача аргументов<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Пример. | Да | Да |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Нет<br>`services.AddSingleton<MyDep>();` | Примеры | Да | Да |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Да<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Пример. | Да | Нет |
| `AddSingleton(new {IMPLEMENTATION})`<br>Нет<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Примеры | Нет | Да |

Да Примеры

Нет

Нет Да

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Дополнительные сведения об удалении типа см. в разделе [Удаление служб](#disposal-of-services).

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Распространенный сценарий для нескольких реализаций — [макеты типов для тестирования](xref:test/integration-tests#inject-mock-services). Методы `TryAdd{LIFETIME}` регистрируют службу только в том случае, если еще не зарегистрирована реализация. В следующем примере первая строка регистрирует `MyDependency` для `IMyDependency`. Вторая строка ничего не делает, поскольку у `IMyDependency` уже есть зарегистрированная реализация:

Дополнительные сведения можно найти в разделе Методы [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) регистрируют службу только в том случае, если еще не существует реализации *того же типа*. Несколько служб разрешается через `IEnumerable<{SERVICE}>`.

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>При регистрации служб разработчик хочет добавить экземпляр только в том случае, если экземпляр такого типа еще не был добавлен.

Как правило, этот метод используется авторами библиотек, чтобы избежать регистрации двух копий экземпляра в контейнере.

* <xref:System.IServiceProvider>
* В следующем примере первая строка регистрирует `MyDep` для `IMyDep1`. Вторая строка регистрирует `MyDep` для `IMyDep2`.

Третья строка ничего не делает, поскольку у `IMyDep1` уже есть зарегистрированная реализация `MyDep`:

Поведение внедрения через конструктор

Службы можно разрешать двумя методами: <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>. Позволяет создавать объекты без регистрации службы в контейнере внедрения зависимостей.

## <a name="entity-framework-contexts"></a>`ActivatorUtilities` используется с абстракциями пользовательского уровня, например со вспомогательными функциями для тегов, контроллерами MVC, и связывателями моделей.

Конструкторы могут принимать аргументы, которые не предоставляются внедрением зависимостей, но эти аргументы должны назначать значения по умолчанию. Когда разрешение служб выполняется через `IServiceProvider` или `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется *открытый* конструктор. Когда разрешение служб выполняется через `ActivatorUtilities`, для [внедрения через конструктор](xref:mvc/controllers/dependency-injection#constructor-injection) требуется наличие только одного соответствующего конструктора.

## <a name="lifetime-and-registration-options"></a>Перегрузки конструктора поддерживаются, но может существовать всего одна перегрузка, все аргументы которой могут быть обработаны с помощью внедрения зависимостей.

Контексты Entity Framework Контексты Entity Framework обычно добавляются в контейнер службы с помощью [времени существования с заданной областью](#service-lifetimes), поскольку операции базы данных в веб-приложении обычно относятся к области клиентского запроса.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Время существования по умолчанию имеет заданную область, если время существования не указано в перегрузке [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) при регистрации контекста базы данных. Службы данного времени существования не должны использовать контекст базы данных с временем существования короче, чем у службы.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Параметры времени существования и регистрации Чтобы продемонстрировать различия между указанными вариантами времени существования и регистрации, рассмотрим интерфейсы, представляющие задачи в виде операции с уникальным идентификатором `OperationId`.

* В зависимости от того, как время существования службы операции настроено для этих интерфейсов, при запросе из класса контейнер предоставляет тот же или другой экземпляр службы. Интерфейсы реализованы в классе `Operation`. Если идентификатор GUID не предоставлен, конструктор `Operation` создает его.
* Регистрируется служба `OperationService`, которая зависит от каждого из других типов `Operation`. Когда служба `OperationService` запрашивается через внедрение зависимостей, она получает либо новый экземпляр каждой службы, либо экземпляр уже существующей службы в зависимости от времени существования зависимой службы.
* Если при запросе из контейнера создаются временные службы, `OperationId` службы `IOperationTransient` отличается от `OperationId` службы `OperationService`.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

`OperationService` получает новый экземпляр класса `IOperationTransient`.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Новый экземпляр возвращает другой идентификатор `OperationId`. Если при каждом клиентском запросе создаются регулируемые службы, идентификатор `OperationId` службы `IOperationScoped` будет таким же, как для службы `OperationService` в клиентском запросе.

В разных клиентских запросах обе службы используют разные значения `OperationId`. Если одинарные службы и службы с одинарным экземпляром создаются один раз и используются во всех клиентских запросах и службах, идентификатор `OperationId` будет одинаковым во всех запросах служб. В `Startup.ConfigureServices` каждый тип добавляется в контейнер согласно его времени существования.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Служба `IOperationSingletonInstance` использует определенный экземпляр с известным идентификатором `Guid.Empty`.

Понятно, когда этот тип используется (его GUID — все нули).

В примере приложения показано время существования объектов в пределах одного и нескольких запросов.

В примере приложения `IndexModel` запрашивает каждый вид типа `IOperation`, а также `OperationService`.  
После этого на странице отображаются все значения `OperationId` службы и класса модели страниц в виде назначенных свойств.  
Результаты двух запросов будут выглядеть так:  
**Первый запрос**

Операции контроллера:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64

Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000  
**Второй запрос**

Операции контроллера:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Операции `OperationService`:

* Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4 Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9
* Instance: 00000000-0000-0000-0000-000000000000
* Проанализируйте, какие значения `OperationId` изменяются в пределах одного и нескольких запросов.

## <a name="call-services-from-main"></a>*Временные* объекты всегда разные.

Временное значение `OperationId` отличается в первом и втором клиентских запросах, а также в обеих операциях `OperationService`. Каждому запросу службы и каждому клиентскому запросу предоставляется новый экземпляр. *Ограниченные по области* объекты одинаковы в рамках клиентского запроса, но различаются для разных запросов.

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

## <a name="scope-validation"></a>*Одинарные* объекты остаются неизменными для всех объектов и запросов независимо от того, предоставляется ли в `Startup.ConfigureServices` экземпляр `Operation`.

Вызов служб из функции main

* Создайте <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> с [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) для разрешения службы с заданной областью в области приложения.
* Этот способ позволит получить доступ к службе с заданной областью при запуске для выполнения задач по инициализации.

В следующем примере показано, как получить контекст для `MyScopedService` в `Program.Main`: Проверка области

Когда приложение выполняется в среде разработки, поставщик службы по умолчанию проверяет следующее: Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно. Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.

Корневой поставщик службы создается при вызове <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.

## <a name="request-services"></a>Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.

Службы с заданной областью удаляются создавшим их контейнером.

Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера. Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.

Для получения дополнительной информации см. <xref:fundamentals/host/web-host#scope-validation>. Службы запросов Службы, доступные в пределах запроса ASP.NET Core из `HttpContext`, предоставляются через коллекцию [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

> [!NOTE]
> Службы запросов — это все настроенные и запрашиваемые в приложении службы.

## <a name="design-services-for-dependency-injection"></a>Когда объекты указывают зависимости, они удовлетворяются за счет типов из `RequestServices`, а не из `ApplicationServices`.

Как правило, использовать эти свойства в приложении напрямую не следует.

* Вместо этого запрашивайте требуемые для классов типы через конструкторы классов и разрешите платформе внедрять зависимости.
* Этот процесс создает классы, которые легче тестировать. Предпочтительнее запрашивать зависимости в качестве параметров конструктора, а не обращаться к коллекции `RequestServices`.
* Проектирование служб для внедрения зависимостей Придерживайтесь следующих рекомендаций:
* Проектируйте службы так, чтобы для получения зависимостей они использовали внедрение зависимостей.

Избегайте статических классов и членов с отслеживанием состояния. Вместо этого следует проектировать приложения для использования отдельных служб, что позволяет избежать создания глобального состояния. Избегайте прямого создания экземпляров зависимых классов внутри служб. Прямое создание экземпляров обязывает использовать в коде определенную реализацию.

### <a name="disposal-of-services"></a>Сделайте классы приложения небольшими, хорошо организованными и удобными в тестировании.

Если класс имеет слишком много внедренных зависимостей, обычно это указывает на то, что у класса слишком много задач и он не соответствует [принципу единственной обязанности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Попробуйте выполнить рефакторинг класса и перенести часть его обязанностей в новый класс.

Помните, что в классах модели страниц Razor Pages и классах контроллера MVC должны преимущественно выполняться задачи, связанные с пользовательским интерфейсом.

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

Бизнес-правила и реализация доступа к данным должны обрабатываться в классах, которые предназначены для [этих целей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

* Удаление служб
* Контейнер вызывает <xref:System.IDisposable.Dispose*> для создаваемых им типов <xref:System.IDisposable>.
* Если экземпляр добавлен в контейнер с помощью пользовательского кода, он не будет удален автоматически.
* В следующем примере службы создаются контейнером службы и автоматически удаляются:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>В следующем примере:

#### <a name="transient-limited-lifetime"></a>Экземпляры службы не создаются контейнером службы.

Платформе неизвестно предполагаемое время жизни службы.

Платформа не удаляет службы автоматически.

* Если службы не удаляются явным образом в коде разработчика, они сохраняются до завершения работы приложения.
* Руководство по применению временных и общих экземпляров IDisposable

Временный экземпляр, ограниченное время существования

**Сценарий** Приложению требуется экземпляр <xref:System.IDisposable> с ограниченным временем существования для реализации любого из следующих сценариев: Экземпляр разрешается в корневой области.

* Экземпляр должен быть удален до завершения области.
* **Решение**

#### <a name="shared-instance-limited-lifetime"></a>Используйте шаблон фабрики для создания экземпляра за пределами родительской области.

В этом случае приложение обычно имеет метод `Create`, который непосредственно вызывает конструктор окончательного типа.

Если окончательный тип имеет другие зависимости, фабрика позволяет:

Получить <xref:System.IServiceProvider> в своем конструкторе.

Использовать <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, чтобы создать экземпляр за пределами контейнера, используя контейнер для его зависимостей. Общий экземпляр, ограниченное время существования **Сценарий** Приложению требуется общий экземпляр <xref:System.IDisposable> в нескольких службах, но для <xref:System.IDisposable> требуется ограниченное время существования.

#### <a name="general-guidelines"></a>**Решение**

* Зарегистрируйте экземпляр с временем существования с заданной областью. Используйте <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> для запуска и создания интерфейса <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.
* Используйте <xref:System.IServiceProvider> области для получения необходимых служб. Удалить область по истечении времени существования.
* Общие рекомендации Не регистрируйте экземпляры <xref:System.IDisposable> с временной областью.
* Вместо этого используйте шаблон фабрики. Не разрешайте временные экземпляры <xref:System.IDisposable> или экземпляры с заданной областью в корневую область.

## <a name="default-service-container-replacement"></a>Единственное исключение — это когда приложение создает или повторно создает и удаляет <xref:System.IServiceProvider>, что не является идеальным вариантом.

Для получения зависимости <xref:System.IDisposable> через DI не требуется, чтобы получатель реализовывал сам интерфейс <xref:System.IDisposable>. Получатель зависимости <xref:System.IDisposable> не должен вызывать <xref:System.IDisposable.Dispose%2A> для этой зависимости.

* Области должны использоваться для управления жизненным циклом служб.
* Области не являются иерархическими, и между ними нет специальной связи.
* Замена стандартного контейнера служб
* Встроенный контейнер служб предназначен для удовлетворения потребностей платформы и большинства клиентских приложений.
* Мы рекомендуем использовать встроенный контейнер, если только не требуется конкретная функциональная возможность, которую он не поддерживает, например:
* Внедрение свойств

Внедрение по имени

* Дочерние контейнеры
* Настраиваемое управление временем существования
* `Func<T>` поддерживает отложенную инициализацию
* Регистрация на основе соглашения
* С приложениями ASP.NET Core можно использовать следующие сторонние контейнеры:
* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html);
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection);

### <a name="thread-safety"></a>[Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions);

[LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection); [Lamar](https://jasperfx.github.io/lamar/);

[Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection); [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a>Потокобезопасность

* Создавайте потокобезопасные одноэлементные службы. Если одноэлементная служба имеет зависимость от временной службы, с учетом характера использования одноэлементной службой к этой временной службе также может предъявляться требование потокобезопасности.

* Фабричный метод одной службы, например второй аргумент для [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), не обязательно должен быть потокобезопасным. Как и конструктор типов (`static`), он гарантированно будет вызываться один раз одним потоком. Рекомендации Разрешение служб на основе `async/await` и `Task` не поддерживается. C# не поддерживает асинхронные конструкторы, поэтому рекомендуем использовать асинхронные методы после асинхронного разрешения службы.

* Не храните данные и конфигурацию непосредственно в контейнере служб.

* Например, обычно не следует добавлять корзину пользователя в контейнер служб.

  * Конфигурация должна использовать [шаблон параметров](xref:fundamentals/configuration/options).

    Аналогичным образом, избегайте объектов "хранения данных", которые служат лишь для доступа к некоторому другому объекту.

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

    Лучше запросить фактический элемент через внедрение зависимостей.

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

  * Не используйте статический доступ к службам (например, не используйте везде [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)).

* Старайтесь не использовать *шаблон обнаружения служб*, который использует разные стратегии [инверсии управления](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

Не вызывайте <xref:System.IServiceProvider.GetService*> для получения экземпляра службы, когда можно использовать внедрение зависимостей: **Неправильно**:

**Правильно**: Избегайте внедрения фабрики, которая разрешает зависимости во время выполнения с помощью <xref:System.IServiceProvider.GetService*>.

## <a name="additional-resources"></a>Не используйте статический доступ к `HttpContext` (например, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* Как и с любыми рекомендациями, у вас могут возникнуть ситуации, когда нужно отступить от одного из правил.
* Исключения возникают редко, — как правило, это особые случаи, связанные с самой платформой.
* Внедрение зависимостей является *альтернативой* для шаблонов доступа к статическим или глобальным объектам.
* Вы не сможете воспользоваться преимуществами внедрения зависимостей, если будете сочетать его с доступом к статическим объектам.
* Дополнительные ресурсы

::: moniker-end
