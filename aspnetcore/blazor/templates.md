---
title: Шаблоны ASP.NET Core Blazor
author: guardrex
description: Сведения о шаблонах приложений Blazor и структуре проекта Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
ms.openlocfilehash: fc2e81cf130732d515fb871227031493e297cf9f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507776"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="7f440-103">Шаблоны ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7f440-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="7f440-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7f440-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7f440-105">Платформа Blazor предоставляет шаблоны разработки приложений для каждой из моделей размещения Blazor:</span><span class="sxs-lookup"><span data-stu-id="7f440-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="7f440-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="7f440-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="7f440-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="7f440-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="7f440-108">Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="7f440-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="7f440-109">Параметры шаблона доступны при передаче параметра `--help` в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new):</span><span class="sxs-lookup"><span data-stu-id="7f440-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="7f440-110">Структура проекта Blazor</span><span class="sxs-lookup"><span data-stu-id="7f440-110">Blazor project structure</span></span>

<span data-ttu-id="7f440-111">Приложение Blazor, создаваемое на основе шаблона проекта Blazor, состоит из следующих файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="7f440-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7f440-112">`Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="7f440-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="7f440-113">[Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="7f440-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="7f440-114">Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="7f440-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="7f440-115">Компонент `App` является корневым компонентом приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7f440-116">Компонент `App` указывается как элемент модели DOM `app` (`<div id="app">Loading...</div>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("#app")`).</span><span class="sxs-lookup"><span data-stu-id="7f440-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="7f440-117">[Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="7f440-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7f440-118">`Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="7f440-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="7f440-119">[Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="7f440-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="7f440-120">Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="7f440-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="7f440-121">Компонент `App` является корневым компонентом приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7f440-122">Компонент `App` указывается как элемент модели DOM `app` (`<app>Loading...</app>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("app")`).</span><span class="sxs-lookup"><span data-stu-id="7f440-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="7f440-123">[Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="7f440-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="7f440-124">`Startup.cs` (Blazor Server): Содержит логику для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="7f440-125">В классе `Startup` определены два метода:</span><span class="sxs-lookup"><span data-stu-id="7f440-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="7f440-126">`ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="7f440-127">В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="7f440-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="7f440-128">`Configure`. Настраивает конвейер обработки запросов для приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="7f440-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером.</span><span class="sxs-lookup"><span data-stu-id="7f440-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="7f440-130">Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="7f440-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (`Pages/_Host.cshtml`) и обеспечения навигации.</span><span class="sxs-lookup"><span data-stu-id="7f440-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="7f440-132">`wwwroot/index.html` (Blazor WebAssembly): Корневая страница приложения в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="7f440-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="7f440-133">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="7f440-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="7f440-134">На странице указывается место отрисовки корневого компонента `App`.</span><span class="sxs-lookup"><span data-stu-id="7f440-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="7f440-135">Компонент отображается в расположении элемента модели DOM `app` (`<app>...</app>`).</span><span class="sxs-lookup"><span data-stu-id="7f440-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="7f440-136">Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7f440-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="7f440-137">скачивает среду выполнения .NET, приложение и его зависимости;</span><span class="sxs-lookup"><span data-stu-id="7f440-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="7f440-138">инициализирует среду выполнения для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="7f440-139">`App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="7f440-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="7f440-140">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.</span><span class="sxs-lookup"><span data-stu-id="7f440-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="7f440-141">Папка `Pages`: содержит маршрутизируемые компоненты и страницы (`.razor`), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7f440-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="7f440-142">Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="7f440-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="7f440-143">Шаблон включает в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="7f440-143">The template includes the following:</span></span>
  * <span data-ttu-id="7f440-144">`_Host.cshtml` (Blazor Server): Корневая страница приложения, реализованная как страница Razor.</span><span class="sxs-lookup"><span data-stu-id="7f440-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="7f440-145">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="7f440-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="7f440-146">Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="7f440-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="7f440-147">На странице Host указывается место отрисовки корневого компонента `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="7f440-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="7f440-148">`Counter` (`Pages/Counter.razor`): Реализует страницу счетчика.</span><span class="sxs-lookup"><span data-stu-id="7f440-148">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="7f440-149">`Error` (`Error.razor` — только для приложения Blazor Server). Отображается, когда в приложении происходит необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="7f440-149">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="7f440-150">`FetchData` (`Pages/FetchData.razor`): Реализует страницу получения данных.</span><span class="sxs-lookup"><span data-stu-id="7f440-150">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="7f440-151">`Index` (`Pages/Index.razor`): Реализует главную страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-151">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="7f440-152">`Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="7f440-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="7f440-153">Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="7f440-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="7f440-154">`MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-154">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7f440-155">`NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации.</span><span class="sxs-lookup"><span data-stu-id="7f440-155">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7f440-156">Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor.</span><span class="sxs-lookup"><span data-stu-id="7f440-156">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7f440-157">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.</span><span class="sxs-lookup"><span data-stu-id="7f440-157">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="7f440-158">`_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.</span><span class="sxs-lookup"><span data-stu-id="7f440-158">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="7f440-159">Папка `Data` (Blazor Server). Содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-159">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="7f440-160">`wwwroot`. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="7f440-160">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="7f440-161">`appsettings.json`. Содержит [параметры конфигурации](xref:blazor/fundamentals/configuration) для приложения.</span><span class="sxs-lookup"><span data-stu-id="7f440-161">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="7f440-162">Для приложения Blazor WebAssembly файл параметров приложения расположен в папке `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="7f440-162">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="7f440-163">Для приложения Blazor Server файл параметров приложения расположен в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="7f440-163">In a Blazor Server app, the app settings file is located at the project root.</span></span>
