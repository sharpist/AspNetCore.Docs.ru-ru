---
title: Привязывание к браузеру в ASP.NET Core
author: ncarandini
description: Узнайте о том, как функция привязывания к браузеру Visual Studio связывает среду разработки с одним или несколькими веб-браузерами.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054493"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="86b40-103">Привязывание к браузеру в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86b40-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="86b40-104">Авторы: [Николо Карандини (Nicolò Carandini)](https://github.com/ncarandini), [Майк Вассон (Mike Wasson)](https://github.com/MikeWasson) и [Том Дайкстра (Tom Dykstra)](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="86b40-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="86b40-105">Привязывание к браузеру является компонентом Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="86b40-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="86b40-106">Оно создает канал связи между средой разработки и одним или несколькими веб-браузерами.</span><span class="sxs-lookup"><span data-stu-id="86b40-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="86b40-107">Можно использовать привязывание к браузеру для обновления веб-приложения одновременно в нескольких браузерах, что полезно для тестирования в разных браузерах.</span><span class="sxs-lookup"><span data-stu-id="86b40-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="86b40-108">Настройка привязывания к браузеру</span><span class="sxs-lookup"><span data-stu-id="86b40-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86b40-109">Добавьте пакет [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) в проект.</span><span class="sxs-lookup"><span data-stu-id="86b40-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="86b40-110">Для проектов ASP.NET Core Razor Pages или MVC также включите компиляцию в среде выполнения Razor (*CSHTML*), как описано в <xref:mvc/views/view-compilation>.</span><span class="sxs-lookup"><span data-stu-id="86b40-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="86b40-111">Изменения синтаксиса Razor применяются только при включенной компиляции среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="86b40-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="86b40-112">При преобразовании проекта ASP.NET Core 2.0 в ASP.NET Core 2.1 и переходе к [метапакету Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) установите пакет [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) для функции привязывания к браузеру.</span><span class="sxs-lookup"><span data-stu-id="86b40-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="86b40-113">Шаблоны проектов ASP.NET Core 2.1 по умолчанию используют метапакет `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="86b40-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="86b40-114">Шаблоны ASP.NET Core 2.0 **Веб-приложение**, **Пустой** и **Веб-API** используют [метапакет Microsoft.AspNetCore.All](xref:fundamentals/metapackage), который содержит ссылку на пакет для [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="86b40-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="86b40-115">Поэтому использование метапакета `Microsoft.AspNetCore.All` не требует дальнейших действий, чтобы сделать привязывание к браузеру доступным для использования.</span><span class="sxs-lookup"><span data-stu-id="86b40-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="86b40-116">Шаблон проекта ASP.NET Core 1.x **Веб-приложение** содержит ссылку на пакет для пакета [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="86b40-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="86b40-117">Для других типов проектов требуется добавить ссылку на пакет `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="86b40-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="86b40-118">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="86b40-118">Configuration</span></span>

<span data-ttu-id="86b40-119">Вызовите `Startup.Configure` в методе `UseBrowserLink`:</span><span class="sxs-lookup"><span data-stu-id="86b40-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="86b40-120">Вызов `UseBrowserLink` обычно размещается в блоке `if`, который разрешает привязывание к браузеру только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="86b40-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="86b40-121">Пример:</span><span class="sxs-lookup"><span data-stu-id="86b40-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="86b40-122">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="86b40-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="86b40-123">Использование привязывания к браузеру</span><span class="sxs-lookup"><span data-stu-id="86b40-123">How to use Browser Link</span></span>

<span data-ttu-id="86b40-124">При открытии проекта ASP.NET Core в Visual Studio элемент управления панели инструментов "Привязывание к браузеру" отображается рядом с элементом управления **Целевой объект отладки**:</span><span class="sxs-lookup"><span data-stu-id="86b40-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Раскрывающееся меню "Привязывание к браузеру"](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="86b40-126">С помощью элемента управления панели инструментов "Привязывание к браузеру" можно:</span><span class="sxs-lookup"><span data-stu-id="86b40-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="86b40-127">Одновременно обновить веб-приложение в нескольких браузерах.</span><span class="sxs-lookup"><span data-stu-id="86b40-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="86b40-128">Открыть **Панель мониторинга "Привязывание к браузеру"** .</span><span class="sxs-lookup"><span data-stu-id="86b40-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="86b40-129">Включить или отключить **привязывание к браузеру**.</span><span class="sxs-lookup"><span data-stu-id="86b40-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="86b40-130">Примечание. По умолчанию в Visual Studio привязывание к браузеру отключено.</span><span class="sxs-lookup"><span data-stu-id="86b40-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="86b40-131">Включить или отключить [Автосинхронизацию в CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="86b40-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="86b40-132">Одновременное обновление веб-приложения в нескольких браузерах</span><span class="sxs-lookup"><span data-stu-id="86b40-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="86b40-133">Чтобы выбрать один веб-браузер для запуска при запуске проекта, используйте раскрывающееся меню в элементе управления панели инструментов **Целевой объект отладки**:</span><span class="sxs-lookup"><span data-stu-id="86b40-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Раскрывающееся меню F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="86b40-135">Чтобы открыть сразу несколько браузеров, выберите **Просмотреть с помощью...** из того же раскрывающегося списка.</span><span class="sxs-lookup"><span data-stu-id="86b40-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="86b40-136">Удерживайте клавишу <kbd>CTRL</kbd>, чтобы выбрать нужные браузеры, а затем щелкните **Обзор**:</span><span class="sxs-lookup"><span data-stu-id="86b40-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Одновременное открытие нескольких браузеров](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="86b40-138">На следующем снимке экрана показана Visual Studio с открытым представлением индекса и двумя открытыми браузерами:</span><span class="sxs-lookup"><span data-stu-id="86b40-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Пример синхронизации с двумя браузерами](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="86b40-140">Наведите указатель мыши на элемент управления панели инструментов "Привязывание к браузеру", чтобы просмотреть браузеры, подключенные к проекту:</span><span class="sxs-lookup"><span data-stu-id="86b40-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Подсказка при наведении](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="86b40-142">Измените представление индекса, и все подключенные браузеры будут обновлены при нажатии кнопки обновления для привязывания к браузеру:</span><span class="sxs-lookup"><span data-stu-id="86b40-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="86b40-144">Привязывание к браузеру также работает с браузерами, которые вы запускаете не в Visual Studio и используете для перехода по URL-адресу приложения.</span><span class="sxs-lookup"><span data-stu-id="86b40-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="86b40-145">Панель мониторинга привязывания к браузеру</span><span class="sxs-lookup"><span data-stu-id="86b40-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="86b40-146">Чтобы управлять соединением с открытыми браузерами, откройте **Панель мониторинга привязывания к браузеру** в раскрывающемся меню "Привязывание к браузеру":</span><span class="sxs-lookup"><span data-stu-id="86b40-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="86b40-148">Если ни один браузер не подключен, можно запустить сеанс без отладки, выбрав **Просмотреть в браузере**:</span><span class="sxs-lookup"><span data-stu-id="86b40-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="86b40-150">В противном случае отображаются подключенные браузеры с указанием пути к странице, отображаемой в каждом браузере:</span><span class="sxs-lookup"><span data-stu-id="86b40-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="86b40-152">Можно также щелкнуть имя отдельного браузера, чтобы обновить только его.</span><span class="sxs-lookup"><span data-stu-id="86b40-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="86b40-153">Включение или отключение привязывания к браузеру</span><span class="sxs-lookup"><span data-stu-id="86b40-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="86b40-154">При повторном включении привязывания к браузеру после его отключения необходимо обновить браузеры, чтобы повторно подключить их.</span><span class="sxs-lookup"><span data-stu-id="86b40-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="86b40-155">Включение или отключение автосинхронизации в CSS</span><span class="sxs-lookup"><span data-stu-id="86b40-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="86b40-156">Когда автоматическая синхронизация в CSS включена, подключенные браузеры автоматически обновляются при внесении любых изменений в файлы CSS.</span><span class="sxs-lookup"><span data-stu-id="86b40-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="86b40-157">Принцип работы</span><span class="sxs-lookup"><span data-stu-id="86b40-157">How it works</span></span>

<span data-ttu-id="86b40-158">Для создания канала связи между Visual Studio и браузером привязывание к браузеру использует [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="86b40-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="86b40-159">Если привязывание к браузеру включено, Visual Studio выступает в качестве сервера SignalR, к которому могут подключаться несколько клиентов (браузеров).</span><span class="sxs-lookup"><span data-stu-id="86b40-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="86b40-160">Привязывание к браузеру также регистрирует компонент ПО промежуточного слоя в конвейере запросов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="86b40-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="86b40-161">Этот компонент внедряет специальные ссылки `<script>` в каждый запрос страницы с сервера.</span><span class="sxs-lookup"><span data-stu-id="86b40-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="86b40-162">Чтобы просмотреть ссылки на скрипты, выберите **Просмотреть источник** в браузере и прокрутите до конца содержимого тега `<body>`:</span><span class="sxs-lookup"><span data-stu-id="86b40-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="86b40-163">Исходные файлы не изменяются.</span><span class="sxs-lookup"><span data-stu-id="86b40-163">Your source files aren't modified.</span></span> <span data-ttu-id="86b40-164">Компонент ПО промежуточного слоя динамически вставляет ссылки на скрипты.</span><span class="sxs-lookup"><span data-stu-id="86b40-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="86b40-165">Так как на стороне браузера используется только код JavaScript, он работает во всех браузерах, которые поддерживаются SignalR, без использования подключаемого модуля браузера.</span><span class="sxs-lookup"><span data-stu-id="86b40-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
