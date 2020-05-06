---
title: ASP.NET Core SignalR узла в фоновых службах
author: bradygaster
description: Узнайте, как отправить сообщения SignalR клиентам из классов .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777297"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR узла в фоновых службах

По [Брейди Гастер](https://twitter.com/bradygaster)

В этой статье приводятся рекомендации по следующим вопросам.

* Размещение SignalR концентраторов с помощью фонового рабочего процесса, размещенного в ASP.NET Core.
* Отправка сообщений на подключенные клиенты из [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).NET Core.

[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Загрузка)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Включить SignalR при запуске

::: moniker range=">= aspnetcore-3.0"

Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core. В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` добавление требуемых служб в уровень поддержки SignalRASP.NET Core внедрения зависимостей (DI). В `Startup.Configure` `MapHub` метод вызывается в `UseEndpoints` обратном вызове для соединения конечных точек концентратора в конвейере запросов ASP.NET Core.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core. В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` добавление требуемых служб в уровень поддержки SignalRASP.NET Core внедрения зависимостей (DI). В `Startup.Configure` `UseSignalR` метод вызывается для подключения конечных точек концентратора в конвейере запросов ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

В предыдущем примере `ClockHub` класс реализует `Hub<T>` класс для создания строго типизированного концентратора. Объект `ClockHub` настроен в `Startup` классе для реагирования на запросы в конечной точке `/hubs/clock`.

Дополнительные сведения о строго типизированных концентраторах см. [в статье SignalR использование концентраторов в для ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Эта функция не ограничена классом [>концентратора\<](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Любой класс, наследующий от [концентратора](xref:Microsoft.AspNetCore.SignalR.Hub), например [динамичуб](xref:Microsoft.AspNetCore.SignalR.DynamicHub), также будет работать.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Интерфейс, используемый строго типизированным `ClockHub` объектом, является `IClock` интерфейсом.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Вызов SignalR концентратора из фоновой службы

Во время запуска `Worker` класс ( `BackgroundService`) включается с помощью. `AddHostedService`

```csharp
services.AddHostedService<Worker>();
```

Так SignalR как также включается на `Startup` этапе, в котором каждый концентратор подключен к отдельной конечной точке в конвейере HTTP-запросов ASP.NET Core, каждый концентратор представляется `IHubContext<T>` на сервере. Используя функции DI ASP.NET Core, другие классы, созданные на уровне размещения, такие как `BackgroundService` классы, классы контроллеров MVC или Razor модели страниц, могут получать ссылки на серверные концентраторы, принимая экземпляры `IHubContext<ClockHub, IClock>` во время создания.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Так как `ExecuteAsync` метод вызывается итеративно в фоновой службе, текущая дата и время сервера отправляются подключенным клиентам с помощью `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Реагирование SignalR на события с помощью фоновых служб

Как и приложение с одним страницам, использующее SignalR клиент JavaScript для или классическое приложение .NET, может использовать <xref:signalr/dotnet-client>с помощью `BackgroundService` , `IHostedService` а также для подключения к SignalR концентраторам и реагирования на события.

`ClockHubClient` Класс реализует `IClock` интерфейс и `IHostedService` интерфейс. Таким образом, его можно включить во `Startup` время непрерывного выполнения и реагирования на события концентратора с сервера.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Во время инициализации объект `ClockHubClient` создает экземпляр класса `HubConnection` и включает `IClock.ShowTime` метод в качестве обработчика для `ShowTime` события концентратора.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

В `IHostedService.StartAsync` реализации `HubConnection` запускается асинхронно.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Во время `IHostedService.StopAsync` выполнения метода объект `HubConnection` уничтожается асинхронно.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [Концентраторы](xref:signalr/hubs)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
* [Строго типизированные концентраторы](xref:signalr/hubs#strongly-typed-hubs)
