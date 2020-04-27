---
title: Отладка ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661604"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="87c07-103">Отладка ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="87c07-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="87c07-104">Дэниэл Рот (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="87c07-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="87c07-105">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="87c07-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="87c07-106">Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="87c07-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="87c07-107">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="87c07-107">Available scenarios include:</span></span>

* <span data-ttu-id="87c07-108">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="87c07-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="87c07-109">Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="87c07-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="87c07-110">Выполните код пошагово (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="87c07-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="87c07-111">Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="87c07-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="87c07-112">На экране *Локальные переменные* просмотрите значения локальных переменных.</span><span class="sxs-lookup"><span data-stu-id="87c07-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="87c07-113">См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="87c07-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="87c07-114">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="87c07-114">For now, you *can't*:</span></span>

* <span data-ttu-id="87c07-115">Проверка массивов.</span><span class="sxs-lookup"><span data-stu-id="87c07-115">Inspect arrays.</span></span>
* <span data-ttu-id="87c07-116">Проверка элементов с помощью наведения указателя мыши.</span><span class="sxs-lookup"><span data-stu-id="87c07-116">Hover to inspect members.</span></span>
* <span data-ttu-id="87c07-117">Пошаговая отладка с заходом в управляемый код или выходом из него.</span><span class="sxs-lookup"><span data-stu-id="87c07-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="87c07-118">Полная поддержка проверки типов значений.</span><span class="sxs-lookup"><span data-stu-id="87c07-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="87c07-119">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="87c07-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="87c07-120">Попадание в точки останова во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="87c07-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="87c07-121">Отладка приложения с помощью рабочей роли службы.</span><span class="sxs-lookup"><span data-stu-id="87c07-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="87c07-122">Мы будем улучшать процесс отладки в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="87c07-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="87c07-123">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="87c07-123">Prerequisites</span></span>

<span data-ttu-id="87c07-124">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="87c07-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="87c07-125">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="87c07-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="87c07-126">Google Chrome (версии 70 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="87c07-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="87c07-127">Включение отладки для Visual Studio и Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87c07-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="87c07-128">Отладка включается автоматически для новых проектов, созданных с помощью шаблона проекта Blazor WebAssembly ASP.NET Core 3.2 (предварительная версия 3) или более поздней версии ([текущая версия 3.2 Preview 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="87c07-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="87c07-129">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл *launchSettings.json* в запускаемом проекте, включив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="87c07-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="87c07-130">После изменения файл *launchSettings.json* должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="87c07-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="87c07-131">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="87c07-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="87c07-132">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="87c07-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="87c07-133">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="87c07-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="87c07-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87c07-134">Visual Studio</span></span>

<span data-ttu-id="87c07-135">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="87c07-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="87c07-136">Убедитесь, что у вас [установлен последний предварительный выпуск Visual Studio 2019 16.6](https://visualstudio.com/preview) (предварительная версия 2 или более поздняя).</span><span class="sxs-lookup"><span data-stu-id="87c07-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="87c07-137">Создайте размещенное приложение Blazor WebAssembly ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87c07-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="87c07-138">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="87c07-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="87c07-139">Установите точку останова в *Counter.razor* в методе `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="87c07-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="87c07-140">Перейдите на вкладку **Счетчик** и нажмите кнопку для попадания в точку останова:</span><span class="sxs-lookup"><span data-stu-id="87c07-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="87c07-142">Просмотрите значение поля `currentCount` в окне локальных переменных:</span><span class="sxs-lookup"><span data-stu-id="87c07-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="87c07-144">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="87c07-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="87c07-145">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:</span><span class="sxs-lookup"><span data-stu-id="87c07-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="87c07-146">Установите точку останова на странице *FetchData.razor* в `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="87c07-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="87c07-147">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="87c07-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="87c07-148">Перейдите на вкладку **Получение данных**, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:</span><span class="sxs-lookup"><span data-stu-id="87c07-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="87c07-150">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="87c07-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="87c07-152">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.</span><span class="sxs-lookup"><span data-stu-id="87c07-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="87c07-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87c07-153">Visual Studio Code</span></span>

<span data-ttu-id="87c07-154">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio Code, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="87c07-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="87c07-155">Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="87c07-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Расширения](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Отладчик предварительной версии JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="87c07-158">Откройте существующее приложение Blazor WebAssembly с включенной отладкой.</span><span class="sxs-lookup"><span data-stu-id="87c07-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="87c07-159">Если вы получаете следующее уведомление о том, что для включения отладки требуется дополнительная настройка, убедитесь, что установлены подходящие расширения и Отладка предварительной версии JavaScript включена, а затем перезагрузите окно:</span><span class="sxs-lookup"><span data-stu-id="87c07-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="87c07-161">Уведомление предлагает добавить необходимые ресурсы в приложение для сборки и отладки.</span><span class="sxs-lookup"><span data-stu-id="87c07-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="87c07-162">Выберите **Да**:</span><span class="sxs-lookup"><span data-stu-id="87c07-162">Select **Yes**:</span></span>

     ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="87c07-164">Запуск приложения в отладчике состоит из двух этапов:</span><span class="sxs-lookup"><span data-stu-id="87c07-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="87c07-165">1\.</span><span class="sxs-lookup"><span data-stu-id="87c07-165">1\.</span></span> <span data-ttu-id="87c07-166">**Сначала** запустите приложение, используя конфигурацию запуска **.NET Core Launch (Blazor Standalone)** (Запуск .NET Core — автономный Blazor).</span><span class="sxs-lookup"><span data-stu-id="87c07-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="87c07-167">2\.</span><span class="sxs-lookup"><span data-stu-id="87c07-167">2\.</span></span> <span data-ttu-id="87c07-168">**После запуска приложения** запустите браузер, используя конфигурацию запуска **.NET Core Debug Blazor Web Assembly in Chrome** (Blazor Web Assembly отладки .NET Core в Chrome) (требуется Chrome).</span><span class="sxs-lookup"><span data-stu-id="87c07-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="87c07-169">Чтобы использовать Edge вместо Chrome, измените `type` конфигурации запуска в *.vscode/launch.json* с `pwa-chrome` на `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="87c07-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="87c07-170">Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:</span><span class="sxs-lookup"><span data-stu-id="87c07-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="87c07-172">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="87c07-172">Debug in the browser</span></span>

1. <span data-ttu-id="87c07-173">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="87c07-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="87c07-174">Нажмите клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="87c07-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="87c07-175">Браузер должен быть запущен с включенной удаленной отладкой.</span><span class="sxs-lookup"><span data-stu-id="87c07-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="87c07-176">Если удаленная отладка отключена, открывается страница ошибки **Не удается найти вкладку браузера для отладки**.</span><span class="sxs-lookup"><span data-stu-id="87c07-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="87c07-177">На странице ошибки содержатся инструкции для запуска браузера с открытым портом отладки, чтобы прокси-сервер отладки Blazor мог подключиться к приложению.</span><span class="sxs-lookup"><span data-stu-id="87c07-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="87c07-178">*Закройте все экземпляры браузера* и перезапустите браузер.</span><span class="sxs-lookup"><span data-stu-id="87c07-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="87c07-179">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш. Через некоторое время на вкладке **Источники** отобразится список сборок .NET в приложении.</span><span class="sxs-lookup"><span data-stu-id="87c07-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="87c07-180">Разверните каждую сборку и найдите исходные файлы *CS*/*RAZOR*, доступные для отладки.</span><span class="sxs-lookup"><span data-stu-id="87c07-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="87c07-181">Установите точки останова, переключитесь обратно на вкладку приложения, и точки останова будут достигнуты при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="87c07-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="87c07-182">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="87c07-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="87c07-183"> предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="87c07-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="87c07-184">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="87c07-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="87c07-185">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="87c07-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="87c07-186">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="87c07-186">Browser source maps</span></span>

<span data-ttu-id="87c07-187">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="87c07-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="87c07-188">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="87c07-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="87c07-189">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="87c07-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="87c07-190">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="87c07-190">Troubleshoot</span></span>

<span data-ttu-id="87c07-191">При возникновении ошибок вам поможет следующий совет:</span><span class="sxs-lookup"><span data-stu-id="87c07-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="87c07-192">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="87c07-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="87c07-193">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="87c07-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
