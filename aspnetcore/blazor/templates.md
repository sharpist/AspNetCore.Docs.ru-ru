---
title: Шаблоны ASP.NET Core Blazor
author: guardrex
description: Сведения о шаблонах приложений Blazor и структуре проекта Blazor в ASP.NET Core.
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
uid: blazor/templates
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102490"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="6f2b9-103">Шаблоны ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6f2b9-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="6f2b9-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f2b9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f2b9-105">Платформа Blazor предоставляет шаблоны разработки приложений для каждой из моделей размещения Blazor:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="6f2b9-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="6f2b9-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="6f2b9-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="6f2b9-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="6f2b9-108">Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="6f2b9-109">Пошаговые инструкции по созданию приложения Blazor на основе шаблона см. в статье <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="6f2b9-110">Параметры шаблона доступны при передаче параметра `--help` в команду CLI [DotNet New](/dotnet/core/tools/dotnet-new):</span><span class="sxs-lookup"><span data-stu-id="6f2b9-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a><span data-ttu-id="6f2b9-111">Структура проекта Blazor</span><span class="sxs-lookup"><span data-stu-id="6f2b9-111">Blazor project structure</span></span>

<span data-ttu-id="6f2b9-112">Приложение Blazor, создаваемое на основе шаблона Blazor, состоит из следующих файлов и папок:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="6f2b9-113">*Program.cs*: это точка входа в приложение, где настраиваются следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-113">*Program.cs*: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="6f2b9-114">[узел](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server);</span><span class="sxs-lookup"><span data-stu-id="6f2b9-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="6f2b9-115">узел WebAssembly (Blazor WebAssembly): код в этом файле используется только для приложений, созданных из шаблона Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="6f2b9-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="6f2b9-116">Компонент `App`, который является корневым компонентом приложения, указывается как элемент `app` модели DOM в методе `Add`.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="6f2b9-117">Службы могут настраиваться с помощью метода `ConfigureServices` построителя узла (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="6f2b9-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="6f2b9-118">Конфигурацию можно предоставить посредством построителя узла (`builder.Configuration`).</span><span class="sxs-lookup"><span data-stu-id="6f2b9-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="6f2b9-119">*Startup.cs* (Blazor Server). Содержит логику для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-119">*Startup.cs* (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="6f2b9-120">В классе `Startup` определены два метода:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="6f2b9-121">`ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="6f2b9-122">В приложениях Blazor Server службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="6f2b9-123">`Configure`. Настраивает конвейер обработки запросов для приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="6f2b9-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="6f2b9-125">Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="6f2b9-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (*Pages/_Host.cshtml*) и обеспечения навигации.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="6f2b9-127">*wwwroot/index.html* (Blazor WebAssembly): Корневая страница приложения в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-127">*wwwroot/index.html* (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="6f2b9-128">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="6f2b9-129">На странице указывается место отрисовки корневого компонента `App`.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="6f2b9-130">Компонент `App` (*App.razor*) указывается как элемент `app` модели DOM в методе `AddComponent` в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="6f2b9-131">Загружается файл JavaScript `_framework/blazor.webassembly.js`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="6f2b9-132">скачивает среду выполнения .NET, приложение и его зависимости;</span><span class="sxs-lookup"><span data-stu-id="6f2b9-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="6f2b9-133">инициализирует среду выполнения для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="6f2b9-134">*App.razor*. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-134">*App.razor*: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="6f2b9-135">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="6f2b9-136">Папка *Pages*. Содержит маршрутизируемые компоненты и страницы ( *.razor*), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-136">*Pages* folder: Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="6f2b9-137">Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="6f2b9-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="6f2b9-138">Шаблон включает в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="6f2b9-138">The template includes the following:</span></span>
  * <span data-ttu-id="6f2b9-139">*_Host.cshtml* (Blazor Server). Корневая страница приложения, реализованная как страница Razor.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-139">*_Host.cshtml* (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="6f2b9-140">При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="6f2b9-141">Загружается файл JavaScript `_framework/blazor.server.js`, который настраивает соединение SignalR в режиме реального времени между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="6f2b9-142">На странице Host указывается место отрисовки корневого компонента `App` (*App.razor*).</span><span class="sxs-lookup"><span data-stu-id="6f2b9-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="6f2b9-143">`Counter` (*Counter.razor*). Реализует страницу счетчика.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-143">`Counter` (*Counter.razor*): Implements the Counter page.</span></span>
  * <span data-ttu-id="6f2b9-144">`Error` (*Error.razor*, только для приложения Blazor Server). Отображается, когда в приложении происходит необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-144">`Error` (*Error.razor*, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="6f2b9-145">`FetchData` (*FetchData.razor*). Реализует страницу получения данных.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-145">`FetchData` (*FetchData.razor*): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="6f2b9-146">`Index` (*Index.razor*). Реализует главную страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-146">`Index` (*Index.razor*): Implements the Home page.</span></span>

* <span data-ttu-id="6f2b9-147">Папка *Shared*. Содержит другие компоненты пользовательского интерфейса ( *.razor*), используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-147">*Shared* folder: Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="6f2b9-148">`MainLayout` (*MainLayout.razor*). Компонент макета приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-148">`MainLayout` (*MainLayout.razor*): The app's layout component.</span></span>
  * <span data-ttu-id="6f2b9-149">`NavMenu` (*NavMenu.razor*). Реализует боковую панель навигации.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-149">`NavMenu` (*NavMenu.razor*): Implements sidebar navigation.</span></span> <span data-ttu-id="6f2b9-150">Включает в себя [компонент NavLink](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-150">Includes the [NavLink component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="6f2b9-151">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="6f2b9-152">*_Imports.razor*. Содержит стандартные директивы Razor, включаемые в компонент приложения ( *.razor*), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-152">*_Imports.razor*: Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="6f2b9-153">Папка *Data* (Blazor Server). Содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-153">*Data* folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="6f2b9-154">*wwwroot*. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-154">*wwwroot*: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="6f2b9-155">*appsettings.json* (Blazor Server). Параметры конфигурации для приложения.</span><span class="sxs-lookup"><span data-stu-id="6f2b9-155">*appsettings.json* (Blazor Server): Configuration settings for the app.</span></span>
