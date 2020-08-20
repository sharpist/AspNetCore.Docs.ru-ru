---
title: Шаблоны ASP.NET Core Blazor
author: guardrex
description: Сведения о шаблонах приложений Blazor и структуре проекта Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: 0069a6d25c1a8edcfaa454623a95702aae58ca22
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625899"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="dd1a6-103">Шаблоны ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="dd1a6-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="dd1a6-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd1a6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd1a6-105">Платформа Blazor предоставляет шаблоны разработки приложений для каждой из моделей размещения Blazor:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="dd1a6-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="dd1a6-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="dd1a6-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="dd1a6-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="dd1a6-108">Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="dd1a6-109">Параметры шаблона доступны при передаче параметра `--help` в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new):</span><span class="sxs-lookup"><span data-stu-id="dd1a6-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="dd1a6-110">Структура проекта Blazor</span><span class="sxs-lookup"><span data-stu-id="dd1a6-110">Blazor project structure</span></span>

<span data-ttu-id="dd1a6-111">Приложение Blazor, создаваемое на основе шаблона проекта Blazor, состоит из следующих файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

* <span data-ttu-id="dd1a6-112">`Program.cs`. это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="dd1a6-113">[Узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="dd1a6-114">Узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="dd1a6-115">Компонент `App` является корневым компонентом приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="dd1a6-116">Компонент `App` указывается как элемент модели DOM `app` (`<app>...</app>`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("app")`).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-116">The `App` component is specified as the `app` DOM element (`<app>...</app>`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="dd1a6-117">[Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

* <span data-ttu-id="dd1a6-118">`Startup.cs` (Blazor Server): Содержит логику для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="dd1a6-119">В классе `Startup` определены два метода:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="dd1a6-120">`ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="dd1a6-121">В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="dd1a6-122">`Configure`. Настраивает конвейер обработки запросов для приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="dd1a6-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="dd1a6-124">Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="dd1a6-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (`Pages/_Host.cshtml`) и обеспечения навигации.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="dd1a6-126">`wwwroot/index.html` (Blazor WebAssembly): Корневая страница приложения в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="dd1a6-127">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="dd1a6-128">На странице указывается место отрисовки корневого компонента `App`.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="dd1a6-129">Компонент отображается в расположении элемента модели DOM `app` (`<app>...</app>`).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-129">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="dd1a6-130">Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="dd1a6-131">скачивает среду выполнения .NET, приложение и его зависимости;</span><span class="sxs-lookup"><span data-stu-id="dd1a6-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="dd1a6-132">инициализирует среду выполнения для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="dd1a6-133">`App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-133">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="dd1a6-134">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="dd1a6-135">Папка `Pages`: содержит маршрутизируемые компоненты и страницы (`.razor`), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-135">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="dd1a6-136">Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="dd1a6-137">Шаблон включает в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="dd1a6-137">The template includes the following:</span></span>
  * <span data-ttu-id="dd1a6-138">`_Host.cshtml` (Blazor Server): Корневая страница приложения, реализованная как страница Razor.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-138">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="dd1a6-139">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="dd1a6-140">Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="dd1a6-141">На странице Host указывается место отрисовки корневого компонента `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-141">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="dd1a6-142">`Counter` (`Pages/Counter.razor`): Реализует страницу счетчика.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-142">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="dd1a6-143">`Error` (`Error.razor` — только для приложения Blazor Server). Отображается, когда в приложении происходит необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-143">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="dd1a6-144">`FetchData` (`Pages/FetchData.razor`): Реализует страницу получения данных.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-144">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="dd1a6-145">`Index` (`Pages/Index.razor`): Реализует главную страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-145">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="dd1a6-146">`Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="dd1a6-146">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="dd1a6-147">Папка `Shared`: Содержит другие компоненты пользовательского интерфейса (`.razor`), используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="dd1a6-148">`MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-148">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="dd1a6-149">`NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="dd1a6-150">Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="dd1a6-151">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="dd1a6-152">`_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="dd1a6-153">Папка `Data` (Blazor Server). Содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="dd1a6-154">`wwwroot`. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="dd1a6-155">`appsettings.json`. Содержит [параметры конфигурации](xref:blazor/fundamentals/configuration) для приложения.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-155">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="dd1a6-156">Для приложения Blazor WebAssembly файл параметров приложения расположен в папке `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-156">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="dd1a6-157">Для приложения Blazor Server файл параметров приложения расположен в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="dd1a6-157">In a Blazor Server app, the app settings file is located at the project root.</span></span>
