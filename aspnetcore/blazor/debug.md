---
title: Отладка в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9fe51b8c7eafdd62cc6fc1a820135d9ee5ff010e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401016"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="59e06-103">Отладка в ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="59e06-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="59e06-104">[Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="59e06-104">[Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="59e06-105">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome).</span><span class="sxs-lookup"><span data-stu-id="59e06-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="59e06-106">Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="59e06-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="59e06-107">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="59e06-107">Available scenarios include:</span></span>

* <span data-ttu-id="59e06-108">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="59e06-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="59e06-109">Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="59e06-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="59e06-110">Выполните код пошагово (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="59e06-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="59e06-111">Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="59e06-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="59e06-112">На экране *Локальные переменные* просмотрите значения локальных переменных.</span><span class="sxs-lookup"><span data-stu-id="59e06-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="59e06-113">См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59e06-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="59e06-114">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="59e06-114">For now, you *can't*:</span></span>

* <span data-ttu-id="59e06-115">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="59e06-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="59e06-116">Попадание в точки останова во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="59e06-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="59e06-117">Мы будем улучшать процесс отладки в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="59e06-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="59e06-118">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="59e06-118">Prerequisites</span></span>

<span data-ttu-id="59e06-119">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="59e06-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="59e06-120">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="59e06-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="59e06-121">Google Chrome (версии 70 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="59e06-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="59e06-122">Включение отладки для Visual Studio и Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59e06-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="59e06-123">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл `launchSettings.json` в запускаемом проекте, добавив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="59e06-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="59e06-124">После изменения файл `launchSettings.json` должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="59e06-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="59e06-125">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="59e06-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="59e06-126">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="59e06-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="59e06-127">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="59e06-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="59e06-128">Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.</span><span class="sxs-lookup"><span data-stu-id="59e06-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="59e06-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59e06-129">Visual Studio</span></span>

<span data-ttu-id="59e06-130">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="59e06-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="59e06-131">Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59e06-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="59e06-132">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="59e06-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="59e06-133">Установите точку останова в `Pages/Counter.razor` в методе `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="59e06-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="59e06-134">Перейдите на вкладку **`Counter`** и нажмите кнопку для попадания в точку останова:</span><span class="sxs-lookup"><span data-stu-id="59e06-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="59e06-136">Просмотрите значение поля `currentCount` в окне локальных переменных:</span><span class="sxs-lookup"><span data-stu-id="59e06-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="59e06-138">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="59e06-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="59e06-139">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:</span><span class="sxs-lookup"><span data-stu-id="59e06-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="59e06-140">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="59e06-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="59e06-141">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="59e06-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="59e06-142">Перейдите на вкладку **`Fetch Data`** , чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:</span><span class="sxs-lookup"><span data-stu-id="59e06-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="59e06-144">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="59e06-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="59e06-146">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.</span><span class="sxs-lookup"><span data-stu-id="59e06-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="59e06-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59e06-147">Visual Studio Code</span></span>

<span data-ttu-id="59e06-148">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio Code, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="59e06-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="59e06-149">Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="59e06-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Расширения](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Отладчик предварительной версии JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="59e06-152">Отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="59e06-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="59e06-153">Откройте автономное приложение Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="59e06-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="59e06-154">Если вы получите следующее уведомление о том, что для включения отладки требуется дополнительная настройка:</span><span class="sxs-lookup"><span data-stu-id="59e06-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="59e06-155">Убедитесь, что у вас установлены требуемые расширения.</span><span class="sxs-lookup"><span data-stu-id="59e06-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="59e06-156">Убедитесь, что отладчик JavaScript включен.</span><span class="sxs-lookup"><span data-stu-id="59e06-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="59e06-157">Перезагрузите окно.</span><span class="sxs-lookup"><span data-stu-id="59e06-157">Reload the window.</span></span>

   ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="59e06-159">Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.</span><span class="sxs-lookup"><span data-stu-id="59e06-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="59e06-160">При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.</span><span class="sxs-lookup"><span data-stu-id="59e06-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Список доступных вариантов отладки](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="59e06-162">Автономное приложение запустится и откроется браузер отладки.</span><span class="sxs-lookup"><span data-stu-id="59e06-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="59e06-163">Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:</span><span class="sxs-lookup"><span data-stu-id="59e06-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="59e06-165">Отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="59e06-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="59e06-166">Откройте размещенное приложение Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="59e06-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="59e06-167">Если для проекта не задана конфигурация запуска, появится следующее уведомление.</span><span class="sxs-lookup"><span data-stu-id="59e06-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="59e06-168">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="59e06-168">Select **Yes**.</span></span>

   ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="59e06-170">В окне выберите проект *Server* в размещенном решении.</span><span class="sxs-lookup"><span data-stu-id="59e06-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="59e06-171">При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.</span><span class="sxs-lookup"><span data-stu-id="59e06-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="59e06-172">Подключение к существующему сеансу отладки</span><span class="sxs-lookup"><span data-stu-id="59e06-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="59e06-173">Чтобы подключиться к выполняющемуся приложению Blazor, создайте файл `launch.json` со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="59e06-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="59e06-174">Подключение к сеансу отладки поддерживается только для автономных приложений.</span><span class="sxs-lookup"><span data-stu-id="59e06-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="59e06-175">Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.</span><span class="sxs-lookup"><span data-stu-id="59e06-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="59e06-176">Параметры конфигурации запуска</span><span class="sxs-lookup"><span data-stu-id="59e06-176">Launch configuration options</span></span>

<span data-ttu-id="59e06-177">Для отладки типа `blazorwasm` поддерживаются следующие параметры конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="59e06-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="59e06-178">Параметр</span><span class="sxs-lookup"><span data-stu-id="59e06-178">Option</span></span>    | <span data-ttu-id="59e06-179">Описание</span><span class="sxs-lookup"><span data-stu-id="59e06-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="59e06-180">Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению Blazor WebAssembly, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению.</span><span class="sxs-lookup"><span data-stu-id="59e06-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="59e06-181">URL-адрес для открытия в браузере при отладке.</span><span class="sxs-lookup"><span data-stu-id="59e06-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="59e06-182">По умолчанию — `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="59e06-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="59e06-183">Браузер для запуска сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="59e06-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="59e06-184">Задайте `edge` или `chrome`.</span><span class="sxs-lookup"><span data-stu-id="59e06-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="59e06-185">По умолчанию — `chrome`.</span><span class="sxs-lookup"><span data-stu-id="59e06-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="59e06-186">Используется для создания журналов из отладчика JS.</span><span class="sxs-lookup"><span data-stu-id="59e06-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="59e06-187">Задано значение `true`, чтобы создавались журналы.</span><span class="sxs-lookup"><span data-stu-id="59e06-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="59e06-188">При запуске и отладке размещенного приложения Blazor WebAssembly необходимо задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="59e06-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="59e06-189">Указывает полный путь к веб-серверу.</span><span class="sxs-lookup"><span data-stu-id="59e06-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="59e06-190">Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="59e06-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="59e06-191">Количество миллисекунд ожидания до подключения сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="59e06-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="59e06-192">По умолчанию — 30 000 миллисекунд (30 секунд).</span><span class="sxs-lookup"><span data-stu-id="59e06-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="59e06-193">Ссылка на исполняемый файл для запуска сервера размещенного приложения.</span><span class="sxs-lookup"><span data-stu-id="59e06-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="59e06-194">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="59e06-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="59e06-195">Рабочий каталог для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="59e06-195">The working directory to launch the app under.</span></span> <span data-ttu-id="59e06-196">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="59e06-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="59e06-197">Переменные среды, которые должны быть предоставлены запущенному процессу.</span><span class="sxs-lookup"><span data-stu-id="59e06-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="59e06-198">Применимо, только если для параметра `hosted` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="59e06-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="59e06-199">Примеры конфигураций запуска</span><span class="sxs-lookup"><span data-stu-id="59e06-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="59e06-200">Запуск и отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="59e06-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="59e06-201">Подключение к выполняющемуся приложению по указанному URL-адресу</span><span class="sxs-lookup"><span data-stu-id="59e06-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="59e06-202">Запуск и отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="59e06-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="59e06-203">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="59e06-203">Debug in the browser</span></span>

1. <span data-ttu-id="59e06-204">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="59e06-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="59e06-205">Нажмите клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="59e06-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="59e06-206">Браузер должен быть запущен с включенной удаленной отладкой.</span><span class="sxs-lookup"><span data-stu-id="59e06-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="59e06-207">Если удаленная отладка отключена, открывается страница ошибки **Не удается найти вкладку браузера для отладки**.</span><span class="sxs-lookup"><span data-stu-id="59e06-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="59e06-208">На странице ошибки содержатся инструкции для запуска браузера с открытым портом отладки, чтобы прокси-сервер отладки Blazor мог подключиться к приложению.</span><span class="sxs-lookup"><span data-stu-id="59e06-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="59e06-209">*Закройте все экземпляры браузера* и перезапустите браузер.</span><span class="sxs-lookup"><span data-stu-id="59e06-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="59e06-210">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш. Через некоторое время на вкладке **Источники** отобразится список сборок .NET в приложении.</span><span class="sxs-lookup"><span data-stu-id="59e06-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="59e06-211">Разверните каждую сборку и найдите исходные файлы `.cs`/`.razor`, доступные для отладки.</span><span class="sxs-lookup"><span data-stu-id="59e06-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="59e06-212">Установите точки останова, переключитесь обратно на вкладку приложения, и точки останова будут достигнуты при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="59e06-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="59e06-213">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="59e06-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="59e06-214"> предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="59e06-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="59e06-215">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="59e06-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="59e06-216">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="59e06-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="59e06-217">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="59e06-217">Browser source maps</span></span>

<span data-ttu-id="59e06-218">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="59e06-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="59e06-219">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="59e06-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="59e06-220">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="59e06-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="59e06-221">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="59e06-221">Troubleshoot</span></span>

<span data-ttu-id="59e06-222">При возникновении ошибок воспользуйтесь следующими советами:</span><span class="sxs-lookup"><span data-stu-id="59e06-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="59e06-223">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="59e06-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="59e06-224">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="59e06-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="59e06-225">Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59e06-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="59e06-226">Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="59e06-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="59e06-227">В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**.</span><span class="sxs-lookup"><span data-stu-id="59e06-227">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="59e06-228">Этот параметр включен в Visual Studio по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="59e06-228">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="59e06-229">Если отладка не работает, проверьте, выбран ли этот параметр.</span><span class="sxs-lookup"><span data-stu-id="59e06-229">If debugging isn't working, confirm that the option is selected.</span></span>
