---
title: Структура проекта ASP.NET Core Blazor
author: guardrex
description: Сведения о структуре проекта приложения Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513537"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="830ca-103">Структура проекта ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="830ca-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="830ca-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="830ca-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="830ca-105">Приложение Blazor, создаваемое на основе шаблона проекта Blazor, состоит из следующих файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="830ca-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="830ca-106">`Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="830ca-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="830ca-107">[Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="830ca-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="830ca-108">Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="830ca-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="830ca-109">Компонент `App` является корневым компонентом приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="830ca-110">Компонент `App` указывается как элемент модели DOM `div` с `id` `app` (`<div id="app">Loading...</div>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("#app")`).</span><span class="sxs-lookup"><span data-stu-id="830ca-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="830ca-111">[Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="830ca-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="830ca-112">`Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="830ca-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="830ca-113">[Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="830ca-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="830ca-114">Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="830ca-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="830ca-115">Компонент `App` является корневым компонентом приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="830ca-116">Компонент `App` указывается как элемент модели DOM `app` (`<app>Loading...</app>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("app")`).</span><span class="sxs-lookup"><span data-stu-id="830ca-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="830ca-117">[Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="830ca-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="830ca-118">`Startup.cs` (Blazor Server): Содержит логику для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="830ca-119">В классе `Startup` определены два метода:</span><span class="sxs-lookup"><span data-stu-id="830ca-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="830ca-120">`ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="830ca-121">В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="830ca-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="830ca-122">`Configure`. Настраивает конвейер обработки запросов для приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="830ca-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером.</span><span class="sxs-lookup"><span data-stu-id="830ca-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="830ca-124">Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="830ca-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (`Pages/_Host.cshtml`) и обеспечения навигации.</span><span class="sxs-lookup"><span data-stu-id="830ca-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="830ca-126">`wwwroot/index.html` (Blazor WebAssembly): Корневая страница приложения в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="830ca-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="830ca-127">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="830ca-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="830ca-128">На странице указывается место отрисовки корневого компонента `App`.</span><span class="sxs-lookup"><span data-stu-id="830ca-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="830ca-129">Компонент отображается в расположении элемента модели DOM `div` с `id` `app` (`<div id="app">Loading...</div>`).</span><span class="sxs-lookup"><span data-stu-id="830ca-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="830ca-130">Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="830ca-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="830ca-131">скачивает среду выполнения .NET, приложение и его зависимости;</span><span class="sxs-lookup"><span data-stu-id="830ca-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="830ca-132">инициализирует среду выполнения для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="830ca-133">`wwwroot/index.html` (Blazor WebAssembly): Корневая страница приложения в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="830ca-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="830ca-134">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="830ca-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="830ca-135">На странице указывается место отрисовки корневого компонента `App`.</span><span class="sxs-lookup"><span data-stu-id="830ca-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="830ca-136">Компонент отображается в расположении элемента модели DOM `app` (`<app>Loading...</app>`).</span><span class="sxs-lookup"><span data-stu-id="830ca-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="830ca-137">Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="830ca-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="830ca-138">скачивает среду выполнения .NET, приложение и его зависимости;</span><span class="sxs-lookup"><span data-stu-id="830ca-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="830ca-139">инициализирует среду выполнения для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="830ca-140">`App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="830ca-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="830ca-141">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.</span><span class="sxs-lookup"><span data-stu-id="830ca-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="830ca-142">Папка `Pages`: содержит маршрутизируемые компоненты и страницы (`.razor`), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="830ca-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="830ca-143">Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="830ca-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="830ca-144">Шаблон включает в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="830ca-144">The template includes the following:</span></span>
  * <span data-ttu-id="830ca-145">`_Host.cshtml` (Blazor Server): Корневая страница приложения, реализованная как страница Razor.</span><span class="sxs-lookup"><span data-stu-id="830ca-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="830ca-146">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="830ca-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="830ca-147">Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="830ca-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="830ca-148">На странице Host указывается место отрисовки корневого компонента `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="830ca-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="830ca-149">Компонент `Counter` (`Pages/Counter.razor`): Реализует страницу счетчика.</span><span class="sxs-lookup"><span data-stu-id="830ca-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="830ca-150">Компонент `Error` (`Error.razor` — только для приложения Blazor Server): Отображается, когда в приложении происходит необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="830ca-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="830ca-151">Компонент `FetchData` (`Pages/FetchData.razor`): Реализует страницу получения данных.</span><span class="sxs-lookup"><span data-stu-id="830ca-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="830ca-152">Компонент `Index` (`Pages/Index.razor`): Реализует главную страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="830ca-153">`Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="830ca-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="830ca-154">Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="830ca-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="830ca-155">Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="830ca-156">`MainLayout.razor.css`: Таблица стилей для основного макета приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="830ca-157">Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации.</span><span class="sxs-lookup"><span data-stu-id="830ca-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="830ca-158">Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor.</span><span class="sxs-lookup"><span data-stu-id="830ca-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="830ca-159">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.</span><span class="sxs-lookup"><span data-stu-id="830ca-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="830ca-160">`NavMenu.razor.css`: Таблица стилей для меню навигации приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="830ca-161">Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="830ca-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="830ca-162">Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="830ca-163">Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации.</span><span class="sxs-lookup"><span data-stu-id="830ca-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="830ca-164">Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor.</span><span class="sxs-lookup"><span data-stu-id="830ca-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="830ca-165">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.</span><span class="sxs-lookup"><span data-stu-id="830ca-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="830ca-166">`_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.</span><span class="sxs-lookup"><span data-stu-id="830ca-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="830ca-167">Папка `Data` (Blazor Server). Содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="830ca-168">`wwwroot`. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="830ca-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="830ca-169">`appsettings.json`. Содержит [параметры конфигурации](xref:blazor/fundamentals/configuration) для приложения.</span><span class="sxs-lookup"><span data-stu-id="830ca-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="830ca-170">Для приложения Blazor WebAssembly файл параметров приложения расположен в папке `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="830ca-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="830ca-171">Для приложения Blazor Server файл параметров приложения расположен в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="830ca-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
