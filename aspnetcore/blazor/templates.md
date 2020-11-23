---
title: Шаблоны ASP.NET Core Blazor
author: guardrex
description: Сведения о шаблонах приложений Blazor и структуре проекта Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
no-loc:
- appsettings.json
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703726"
---
# <a name="aspnet-core-no-locblazor-templates"></a>Шаблоны ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

Платформа Blazor предоставляет шаблоны разработки приложений для каждой из моделей размещения Blazor:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.

Параметры шаблона доступны при передаче параметра `--help` в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new):

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Структура проекта Blazor

Приложение Blazor, создаваемое на основе шаблона проекта Blazor, состоит из следующих файлов и папок:

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:

  * [Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).
  * Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).
    * Компонент `App` является корневым компонентом приложения. Компонент `App` указывается как элемент модели DOM `app` (`<div id="app">Loading...</div>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("#app")`).
    * [Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:

  * [Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).
  * Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).
    * Компонент `App` является корневым компонентом приложения. Компонент `App` указывается как элемент модели DOM `app` (`<app>Loading...</app>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("app")`).
    * [Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

* `Startup.cs` (Blazor Server): Содержит логику для запуска приложения. В классе `Startup` определены два метода:

  * `ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения. В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.
  * `Configure`. Настраивает конвейер обработки запросов для приложения.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером. Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (`Pages/_Host.cshtml`) и обеспечения навигации.

* `wwwroot/index.html` (Blazor WebAssembly): Корневая страница приложения в формате HTML.
  * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
  * На странице указывается место отрисовки корневого компонента `App`. Компонент отображается в расположении элемента модели DOM `app` (`<app>...</app>`).
  * Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:
    * скачивает среду выполнения .NET, приложение и его зависимости;
    * инициализирует среду выполнения для запуска приложения.

* `App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>. Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.

* Папка `Pages`: содержит маршрутизируемые компоненты и страницы (`.razor`), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server. Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page). Шаблон включает в себя следующее:
  * `_Host.cshtml` (Blazor Server): Корневая страница приложения, реализованная как страница Razor.
    * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
    * Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.
    * На странице Host указывается место отрисовки корневого компонента `App` (`App.razor`).
  * Компонент `Counter` (`Pages/Counter.razor`): Реализует страницу счетчика.
  * Компонент `Error` (`Error.razor` — только для приложения Blazor Server): Отображается, когда в приложении происходит необработанное исключение.
  * Компонент `FetchData` (`Pages/FetchData.razor`): Реализует страницу получения данных.
  * Компонент `Index` (`Pages/Index.razor`): Реализует главную страницу приложения.
  
* `Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * `MainLayout.razor.css`: Таблица стилей для основного макета приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  * `NavMenu.razor.css`: Таблица стилей для меню навигации приложения.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  
::: moniker-end

* `_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.

* Папка `Data` (Blazor Server). Содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.

* `wwwroot`. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.

* `appsettings.json`. Содержит [параметры конфигурации](xref:blazor/fundamentals/configuration) для приложения. Для приложения Blazor WebAssembly файл параметров приложения расположен в папке `wwwroot`. Для приложения Blazor Server файл параметров приложения расположен в корне проекта.
