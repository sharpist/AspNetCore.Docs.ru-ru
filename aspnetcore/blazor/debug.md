---
title: Отладка в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: cb0a8737fb975db285986d18b995e488f09580e8
ms.sourcegitcommit: 37f6f2e13ceb4eae268d20973d76e4b83acf6a24
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526294"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="9a737-103">Отладка в ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9a737-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9a737-104">[Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="9a737-104">[Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9a737-105">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome).</span><span class="sxs-lookup"><span data-stu-id="9a737-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="9a737-106">Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9a737-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="9a737-107">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="9a737-107">Available scenarios include:</span></span>

* <span data-ttu-id="9a737-108">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="9a737-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="9a737-109">Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a737-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="9a737-110">Выполните код пошагово (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a737-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="9a737-111">Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9a737-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="9a737-112">На экране *Локальные переменные* просмотрите значения локальных переменных.</span><span class="sxs-lookup"><span data-stu-id="9a737-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="9a737-113">См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9a737-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="9a737-114">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="9a737-114">For now, you *can't*:</span></span>

* <span data-ttu-id="9a737-115">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="9a737-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="9a737-116">Попадание в точки останова во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="9a737-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="9a737-117">Мы будем улучшать процесс отладки в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="9a737-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9a737-118">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9a737-118">Prerequisites</span></span>

<span data-ttu-id="9a737-119">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="9a737-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="9a737-120">Google Chrome (версии 70 и более поздних) (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="9a737-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="9a737-121">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="9a737-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="9a737-122">Включение отладки для Visual Studio и Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a737-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="9a737-123">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл `launchSettings.json` в запускаемом проекте, добавив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="9a737-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="9a737-124">После изменения файл `launchSettings.json` должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="9a737-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="9a737-125">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="9a737-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="9a737-126">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9a737-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="9a737-127">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="9a737-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="9a737-128">Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.</span><span class="sxs-lookup"><span data-stu-id="9a737-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="9a737-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a737-129">Visual Studio</span></span>

<span data-ttu-id="9a737-130">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="9a737-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="9a737-131">Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a737-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="9a737-132">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="9a737-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="9a737-133">Установите точку останова в `Pages/Counter.razor` в методе `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="9a737-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="9a737-134">Перейдите на вкладку **`Counter`** и нажмите кнопку для попадания в точку останова:</span><span class="sxs-lookup"><span data-stu-id="9a737-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="9a737-136">Просмотрите значение поля `currentCount` в окне локальных переменных:</span><span class="sxs-lookup"><span data-stu-id="9a737-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="9a737-138">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="9a737-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="9a737-139">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:</span><span class="sxs-lookup"><span data-stu-id="9a737-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="9a737-140">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="9a737-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9a737-141">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="9a737-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9a737-142">Перейдите на вкладку **`Fetch Data`** , чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:</span><span class="sxs-lookup"><span data-stu-id="9a737-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="9a737-144">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="9a737-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="9a737-146">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.</span><span class="sxs-lookup"><span data-stu-id="9a737-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="9a737-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a737-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="9a737-148">Отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9a737-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="9a737-149">Откройте автономное приложение Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a737-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="9a737-150">Вы можете получить следующее уведомление о том, что для включения отладки требуется дополнительная настройка:</span><span class="sxs-lookup"><span data-stu-id="9a737-150">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="9a737-152">Если вы получили уведомление:</span><span class="sxs-lookup"><span data-stu-id="9a737-152">If you receive the notification:</span></span>

   * <span data-ttu-id="9a737-153">Убедитесь, что установлена актуальная версия [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="9a737-153">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="9a737-154">Чтобы проверить установленные расширения, откройте раздел **Вид** > **Расширения** в строке меню или выберите значок **Расширения** на боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="9a737-154">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="9a737-155">Убедитесь, что отладчик JavaScript включен.</span><span class="sxs-lookup"><span data-stu-id="9a737-155">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="9a737-156">Откройте файл параметров в строке меню (**Файл** > **Настройки** > **Параметры**).</span><span class="sxs-lookup"><span data-stu-id="9a737-156">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="9a737-157">Выполните поиск по ключевым словам `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="9a737-157">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="9a737-158">В результатах поиска убедитесь, что флажок **Отладка > JavaScript: Use Preview (JavaScript: использовать предварительную версию)** установлен.</span><span class="sxs-lookup"><span data-stu-id="9a737-158">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span>
   * <span data-ttu-id="9a737-159">Перезагрузите окно.</span><span class="sxs-lookup"><span data-stu-id="9a737-159">Reload the window.</span></span>

1. <span data-ttu-id="9a737-160">Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.</span><span class="sxs-lookup"><span data-stu-id="9a737-160">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="9a737-161">При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.</span><span class="sxs-lookup"><span data-stu-id="9a737-161">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Список доступных вариантов отладки](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="9a737-163">Автономное приложение запустится и откроется браузер отладки.</span><span class="sxs-lookup"><span data-stu-id="9a737-163">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="9a737-164">Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:</span><span class="sxs-lookup"><span data-stu-id="9a737-164">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="9a737-166">Отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9a737-166">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="9a737-167">Откройте папку решения размещенного приложения Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a737-167">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="9a737-168">Если для проекта не задана конфигурация запуска, появится следующее уведомление.</span><span class="sxs-lookup"><span data-stu-id="9a737-168">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="9a737-169">Выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="9a737-169">Select **Yes**.</span></span>

   ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="9a737-171">В палитре команд вверху окна выберите проект *Server* в размещенном решении.</span><span class="sxs-lookup"><span data-stu-id="9a737-171">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="9a737-172">При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.</span><span class="sxs-lookup"><span data-stu-id="9a737-172">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="9a737-173">Подключение к существующему сеансу отладки</span><span class="sxs-lookup"><span data-stu-id="9a737-173">Attach to an existing debugging session</span></span>

<span data-ttu-id="9a737-174">Чтобы подключиться к выполняющемуся приложению Blazor, создайте файл `launch.json` со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="9a737-174">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="9a737-175">Подключение к сеансу отладки поддерживается только для автономных приложений.</span><span class="sxs-lookup"><span data-stu-id="9a737-175">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="9a737-176">Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a737-176">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="9a737-177">Параметры конфигурации запуска</span><span class="sxs-lookup"><span data-stu-id="9a737-177">Launch configuration options</span></span>

<span data-ttu-id="9a737-178">Для отладки типа `blazorwasm` (`.vscode/launch.json`) поддерживаются следующие параметры конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="9a737-178">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="9a737-179">Параметр</span><span class="sxs-lookup"><span data-stu-id="9a737-179">Option</span></span>    | <span data-ttu-id="9a737-180">Описание</span><span class="sxs-lookup"><span data-stu-id="9a737-180">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="9a737-181">Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению Blazor WebAssembly, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению.</span><span class="sxs-lookup"><span data-stu-id="9a737-181">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="9a737-182">URL-адрес для открытия в браузере при отладке.</span><span class="sxs-lookup"><span data-stu-id="9a737-182">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="9a737-183">По умолчанию — `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a737-183">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="9a737-184">Браузер для запуска сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="9a737-184">The browser to launch for the debugging session.</span></span> <span data-ttu-id="9a737-185">Задайте `edge` или `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a737-185">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="9a737-186">По умолчанию — `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a737-186">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="9a737-187">Используется для создания журналов из отладчика JS.</span><span class="sxs-lookup"><span data-stu-id="9a737-187">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="9a737-188">Задано значение `true`, чтобы создавались журналы.</span><span class="sxs-lookup"><span data-stu-id="9a737-188">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="9a737-189">При запуске и отладке размещенного приложения Blazor WebAssembly необходимо задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="9a737-189">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="9a737-190">Указывает полный путь к веб-серверу.</span><span class="sxs-lookup"><span data-stu-id="9a737-190">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="9a737-191">Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="9a737-191">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="9a737-192">Количество миллисекунд ожидания до подключения сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="9a737-192">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="9a737-193">По умолчанию — 30 000 миллисекунд (30 секунд).</span><span class="sxs-lookup"><span data-stu-id="9a737-193">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="9a737-194">Ссылка на исполняемый файл для запуска сервера размещенного приложения.</span><span class="sxs-lookup"><span data-stu-id="9a737-194">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="9a737-195">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="9a737-195">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="9a737-196">Рабочий каталог для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="9a737-196">The working directory to launch the app under.</span></span> <span data-ttu-id="9a737-197">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="9a737-197">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="9a737-198">Переменные среды, которые должны быть предоставлены запущенному процессу.</span><span class="sxs-lookup"><span data-stu-id="9a737-198">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="9a737-199">Применимо, только если для параметра `hosted` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="9a737-199">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="9a737-200">Примеры конфигураций запуска</span><span class="sxs-lookup"><span data-stu-id="9a737-200">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="9a737-201">Запуск и отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9a737-201">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="9a737-202">Подключение к выполняющемуся приложению по указанному URL-адресу</span><span class="sxs-lookup"><span data-stu-id="9a737-202">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="9a737-203">Запуск и отладка размещенного приложения Blazor WebAssembly с помощью Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9a737-203">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="9a737-204">По умолчанию конфигурация браузера использует Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="9a737-204">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="9a737-205">При использовании Microsoft Edge для отладки задайте для параметра `browser` значение `edge`.</span><span class="sxs-lookup"><span data-stu-id="9a737-205">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="9a737-206">Чтобы использовать Google Chrome, не задавайте параметр `browser` или задайте для него значение `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a737-206">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="9a737-207">В предыдущем примере `MyHostedApp.Server.dll` является сборкой приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="9a737-207">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="9a737-208">Папка `.vscode` расположена в папке решения рядом с папками `Client`, `Server` и `Shared`.</span><span class="sxs-lookup"><span data-stu-id="9a737-208">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="9a737-209">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="9a737-209">Debug in the browser</span></span>

1. <span data-ttu-id="9a737-210">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="9a737-210">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="9a737-211">Запустите браузер и перейдите по URL-адресу приложения (например, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="9a737-211">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="9a737-212">В браузере попытайтесь запустить удаленную отладку, нажав клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="9a737-212">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="9a737-213">Браузер должен работать с включенной удаленной отладкой (это не поведение по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="9a737-213">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="9a737-214">Если удаленная отладка отключена, отобразится страница с ошибкой **Не удается найти вкладку браузера для отладки** с инструкциями по запуску браузера с открытым портом отладки.</span><span class="sxs-lookup"><span data-stu-id="9a737-214">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="9a737-215">Выполните инструкции в браузере, после чего откроется новое окно браузера.</span><span class="sxs-lookup"><span data-stu-id="9a737-215">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="9a737-216">Закройте предыдущее окно.</span><span class="sxs-lookup"><span data-stu-id="9a737-216">Close the previous browser window.</span></span>

1. <span data-ttu-id="9a737-217">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью клавиш <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="9a737-217">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="9a737-218">Через некоторое время на вкладке **Источники** отобразится список сборок .NET приложения в узле `file://`.</span><span class="sxs-lookup"><span data-stu-id="9a737-218">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="9a737-219">Точки остановка, заданные в коде компонента (файлы `.razor`) и в файлах с кодом C# (`.cs`), будут срабатывать при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="9a737-219">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="9a737-220">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a737-220">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="9a737-221">Blazor предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="9a737-221">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="9a737-222">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="9a737-222">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="9a737-223">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="9a737-223">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="9a737-224">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="9a737-224">Browser source maps</span></span>

<span data-ttu-id="9a737-225">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="9a737-225">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="9a737-226">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="9a737-226">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="9a737-227">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="9a737-227">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9a737-228">Диагностика</span><span class="sxs-lookup"><span data-stu-id="9a737-228">Troubleshoot</span></span>

<span data-ttu-id="9a737-229">При возникновении ошибок воспользуйтесь следующими советами:</span><span class="sxs-lookup"><span data-stu-id="9a737-229">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="9a737-230">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="9a737-230">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="9a737-231">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="9a737-231">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="9a737-232">Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a737-232">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="9a737-233">Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="9a737-233">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="9a737-234">В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**.</span><span class="sxs-lookup"><span data-stu-id="9a737-234">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="9a737-235">Этот параметр включен в Visual Studio по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9a737-235">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="9a737-236">Если отладка не работает, проверьте, выбран ли этот параметр.</span><span class="sxs-lookup"><span data-stu-id="9a737-236">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="9a737-237">Точки останова в `OnInitialized{Async}` не срабатывают</span><span class="sxs-lookup"><span data-stu-id="9a737-237">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="9a737-238">Прокси-сервер платформы Blazor для отладки запускается не мгновенно, поэтому точки останова в[методе жизненного цикла `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) могут не срабатывать.</span><span class="sxs-lookup"><span data-stu-id="9a737-238">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="9a737-239">Мы рекомендуем добавить задержку в начале тела метода, чтобы прокси-сервер отладки успел запуститься до срабатывания точки останова.</span><span class="sxs-lookup"><span data-stu-id="9a737-239">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="9a737-240">Вы можете включить задержку на основе [директивы компилятора `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), чтобы обеспечить отсутствие задержки для конечной сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="9a737-240">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="9a737-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="9a737-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="9a737-242"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="9a737-242"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```
