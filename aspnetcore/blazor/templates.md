---
title: Шаблоны ASP.NET Core Blazor
author: guardrex
description: Сведения о шаблонах приложений Blazor и структуре проекта Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967432"
---
# <a name="aspnet-core-blazor-templates"></a>Шаблоны ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Платформа Blazor предоставляет шаблоны разработки приложений для каждой из моделей размещения Blazor:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.

Пошаговые инструкции по созданию приложения Blazor на основе шаблона см. в статье <xref:blazor/get-started>.

Параметры шаблона доступны при передаче параметра `--help` в команду CLI [DotNet New](/dotnet/core/tools/dotnet-new):

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Структура проекта Blazor

Приложение Blazor, создаваемое на основе шаблона Blazor, состоит из следующих файлов и папок:

* *Program.cs* &ndash; точка входа в приложение, которая служит для настройки следующих компонентов:

  * [узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server);
  * узел WebAssembly (Blazor WebAssembly) &ndash; код в этом файле уникален для приложений, создаваемых на основе шаблона Blazor WebAssembly (`blazorwasm`).
    * Компонент `App`, который является корневым компонентом приложения, указывается как элемент `app` модели DOM в методе `Add`.
    * Службы могут настраиваться с помощью метода `ConfigureServices` построителя узла (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Конфигурацию можно предоставить посредством построителя узла (`builder.Configuration`).

* *Startup.cs* (Blazor Server) &ndash; содержит логику запуска приложения. В классе `Startup` определены два метода:

  * `ConfigureServices` &ndash; настраивает службы [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection) для приложения. В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.
  * `Configure` &ndash; настраивает конвейер обработки запросов для приложения.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером. Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (*Pages/_Host.cshtml*) и обеспечения навигации.

* *wwwroot/index.html* (Blazor WebAssembly) &ndash; корневая страница приложения, реализованная как HTML-страница.
  * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
  * На странице указывается место отрисовки корневого компонента `App`. Компонент `App` (*App.razor*) указывается как элемент `app` модели DOM в методе `AddComponent` в `Startup.Configure`.
  * Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:
    * скачивает среду выполнения .NET, приложение и его зависимости;
    * инициализирует среду выполнения для запуска приложения.

* *App.razor* &ndash; корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>. Компонент `Router` перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.

* Папка *Pages* &ndash; содержит маршрутизируемые компоненты и страницы ( *.razor*), составляющие приложение Blazor, а также корневую страницу Razor приложения Blazor Server. Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page). Шаблон включает в себя следующее:
  * *_Host.cshtml* (Blazor Server) &ndash;. Корневая страница приложения, реализованная как страница Razor.
    * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
    * Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.
    * На странице Host указывается место отрисовки корневого компонента `App` (*App.razor*).
  * `Counter` (*Counter.razor*) &ndash; реализует страницу Counter;
  * `Error` (*Error.razor*, только в приложении Blazor Server) &ndash; отрисовывается, когда в приложении возникает необработанное исключение;
  * `FetchData` (*FetchData.razor*) &ndash; реализует страницу Fetch data.
  * `Index` (*Index.razor*) &ndash; реализует страницу Home;

* Папка *Shared* &ndash; содержит другие компоненты пользовательского интерфейса ( *.razor*), используемые приложением:
  * `MainLayout` (*MainLayout.razor*) &ndash; компонент макета приложения;
  * `NavMenu` (*NavMenu.razor*) &ndash; реализует боковую панель навигации. Включает в себя [компонент NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент `NavLink` автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.

* *_Imports.razor* &ndash; содержит стандартные директивы Razor, включаемые в компонент приложения ( *.razor*), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.

* Папка *Data* (Blazor Server) &ndash; содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют образец метеоданных для компонента `FetchData` приложения.

* *wwwroot* &ndash; папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит его открытые статические ресурсы.

* *appsettings.json* (Blazor Server) &ndash; параметры конфигурации приложения.
