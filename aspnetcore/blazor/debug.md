---
title: "title: 'Отладка ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Узнайте, как отлаживать приложения Blazor.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 31.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239204"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="5a067-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a067-103">'Blazor'</span></span>

<span data-ttu-id="5a067-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a067-104">'Identity'</span></span>

<span data-ttu-id="5a067-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a067-105">'Let's Encrypt'</span></span> <span data-ttu-id="5a067-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a067-106">'Razor'</span></span>

<span data-ttu-id="5a067-107">'SignalR' uid: blazor/debug</span><span class="sxs-lookup"><span data-stu-id="5a067-107">'SignalR' uid: blazor/debug</span></span>

* <span data-ttu-id="5a067-108">Отладка ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5a067-108">Debug ASP.NET Core Blazor WebAssembly</span></span>
* [<span data-ttu-id="5a067-109">Дэниэл Рот (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="5a067-109">Daniel Roth</span></span>](https://github.com/danroth27)
* <span data-ttu-id="5a067-110">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="5a067-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>
* <span data-ttu-id="5a067-111">Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5a067-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="5a067-112">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="5a067-112">Available scenarios include:</span></span>
* <span data-ttu-id="5a067-113">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="5a067-113">Set and remove breakpoints.</span></span>

<span data-ttu-id="5a067-114">Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="5a067-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>

* <span data-ttu-id="5a067-115">Выполните код пошагово (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="5a067-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="5a067-116">Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5a067-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="5a067-117">На экране *Локальные переменные* просмотрите значения локальных переменных.</span><span class="sxs-lookup"><span data-stu-id="5a067-117">In the *Locals* display, observe the values of local variables.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a067-118">См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5a067-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="5a067-119">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="5a067-119">For now, you *can't*:</span></span>

* <span data-ttu-id="5a067-120">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="5a067-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="5a067-121">Попадание в точки останова во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="5a067-121">Hit breakpoints during app startup.</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="5a067-122">Мы будем улучшать процесс отладки в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="5a067-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

<span data-ttu-id="5a067-123">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="5a067-123">Prerequisites</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="5a067-124">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="5a067-124">Debugging requires either of the following browsers:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="5a067-125">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="5a067-125">Microsoft Edge (version 80 or later)</span></span>

* <span data-ttu-id="5a067-126">Google Chrome (версии 70 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="5a067-126">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="5a067-127">Включение отладки для Visual Studio и Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a067-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="5a067-128">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл *launchSettings.json* в запускаемом проекте, включив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="5a067-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

## <a name="visual-studio"></a><span data-ttu-id="5a067-129">После изменения файл *launchSettings.json* должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5a067-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

<span data-ttu-id="5a067-130">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="5a067-130">The `inspectUri` property:</span></span>

1. <span data-ttu-id="5a067-131">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="5a067-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="5a067-132">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="5a067-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>
1. <span data-ttu-id="5a067-133">Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.</span><span class="sxs-lookup"><span data-stu-id="5a067-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>
1. <span data-ttu-id="5a067-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a067-134">Visual Studio</span></span>

   ![Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="5a067-136">Создайте размещенное приложение Blazor WebAssembly ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a067-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>

   ![Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="5a067-138">Установите точку останова в *Counter.razor* в методе `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="5a067-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>

<span data-ttu-id="5a067-139">Перейдите на вкладку **Счетчик** и нажмите кнопку для попадания в точку останова:</span><span class="sxs-lookup"><span data-stu-id="5a067-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

1. <span data-ttu-id="5a067-140">Счетчик отладки</span><span class="sxs-lookup"><span data-stu-id="5a067-140">Debug Counter</span></span>
1. <span data-ttu-id="5a067-141">Просмотрите значение поля `currentCount` в окне локальных переменных:</span><span class="sxs-lookup"><span data-stu-id="5a067-141">Check out the value of the `currentCount` field in the locals window:</span></span>
1. <span data-ttu-id="5a067-142">Просмотр локальных переменных</span><span class="sxs-lookup"><span data-stu-id="5a067-142">View locals</span></span>

   ![Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="5a067-144">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:</span><span class="sxs-lookup"><span data-stu-id="5a067-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

   ![Установите точку останова на странице *FetchData.razor* в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="5a067-146">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="5a067-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="5a067-147">Перейдите на вкладку **Получение данных**, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:</span><span class="sxs-lookup"><span data-stu-id="5a067-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

<span data-ttu-id="5a067-148">Отладка получения данных</span><span class="sxs-lookup"><span data-stu-id="5a067-148">Debug Fetch Data</span></span>
 
<span data-ttu-id="5a067-149">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="5a067-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="5a067-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a067-152">Visual Studio Code</span></span>

1. <span data-ttu-id="5a067-153">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio Code, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="5a067-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>

   <span data-ttu-id="5a067-154">Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="5a067-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>
   
   * <span data-ttu-id="5a067-155">Расширения</span><span class="sxs-lookup"><span data-stu-id="5a067-155">Extensions</span></span>
   * <span data-ttu-id="5a067-156">Отладчик предварительной версии JS</span><span class="sxs-lookup"><span data-stu-id="5a067-156">JS preview debugger</span></span>
   * <span data-ttu-id="5a067-157">Отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5a067-157">Debug standalone Blazor WebAssembly</span></span>

   ![Откройте автономное приложение Blazor WebAssembly в VS Code.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="5a067-159">Если вы получите следующее уведомление о том, что для включения отладки требуется дополнительная настройка:</span><span class="sxs-lookup"><span data-stu-id="5a067-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>

1. <span data-ttu-id="5a067-160">Убедитесь, что у вас установлены требуемые расширения.</span><span class="sxs-lookup"><span data-stu-id="5a067-160">Confirm that you have the correct extensions installed.</span></span>

   ![Убедитесь, что отладчик JavaScript включен.](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="5a067-162">Перезагрузите окно.</span><span class="sxs-lookup"><span data-stu-id="5a067-162">Reload the window.</span></span>

1. <span data-ttu-id="5a067-163">Требуется дополнительная настройка</span><span class="sxs-lookup"><span data-stu-id="5a067-163">Additional setup required</span></span>

   ![Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="5a067-165">При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.</span><span class="sxs-lookup"><span data-stu-id="5a067-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="5a067-166">Список доступных вариантов отладки</span><span class="sxs-lookup"><span data-stu-id="5a067-166">List of available debug options</span></span>

1. <span data-ttu-id="5a067-167">Автономное приложение запустится и откроется браузер отладки.</span><span class="sxs-lookup"><span data-stu-id="5a067-167">The standalone app is launched, and a debugging browser is opened.</span></span> <span data-ttu-id="5a067-168">Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:</span><span class="sxs-lookup"><span data-stu-id="5a067-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="5a067-170">Отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5a067-170">Debug hosted Blazor WebAssembly</span></span>

<span data-ttu-id="5a067-171">Откройте размещенное приложение Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a067-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="5a067-172">Если для проекта не задана конфигурация запуска, появится следующее уведомление.</span><span class="sxs-lookup"><span data-stu-id="5a067-172">If there's no launch configuration set for the project, the following notification appears.</span></span>

<span data-ttu-id="5a067-173">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="5a067-173">Select **Yes**.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="5a067-174">Добавление необходимых ресурсов</span><span class="sxs-lookup"><span data-stu-id="5a067-174">Add required assets</span></span> <span data-ttu-id="5a067-175">В окне выберите проект *Server* в размещенном решении.</span><span class="sxs-lookup"><span data-stu-id="5a067-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="5a067-176">При этом будет создан файл *launch.json* с конфигурацией запуска для отладчика.</span><span class="sxs-lookup"><span data-stu-id="5a067-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

<span data-ttu-id="5a067-177">Подключение к существующему сеансу отладки</span><span class="sxs-lookup"><span data-stu-id="5a067-177">Attach to an existing debugging session</span></span>

| <span data-ttu-id="5a067-178">Чтобы подключиться к выполняющемуся приложению Blazor, создайте файл *launch.json* со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="5a067-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>    | <span data-ttu-id="5a067-179">Подключение к сеансу отладки поддерживается только для автономных приложений.</span><span class="sxs-lookup"><span data-stu-id="5a067-179">Attaching to a debugging session is only supported for standalone apps.</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="5a067-180">Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a067-180">To use full-stack debugging, you must launch the app from VS Code.</span></span> |
| `url`     | <span data-ttu-id="5a067-181">Параметры конфигурации запуска</span><span class="sxs-lookup"><span data-stu-id="5a067-181">Launch configuration options</span></span> <span data-ttu-id="5a067-182">Для отладки типа `blazorwasm` поддерживаются следующие параметры конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="5a067-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span> |
| `browser` | <span data-ttu-id="5a067-183">Параметр</span><span class="sxs-lookup"><span data-stu-id="5a067-183">Option</span></span> <span data-ttu-id="5a067-184">Описание</span><span class="sxs-lookup"><span data-stu-id="5a067-184">Description</span></span> <span data-ttu-id="5a067-185">Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению Blazor WebAssembly, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению.</span><span class="sxs-lookup"><span data-stu-id="5a067-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `trace`   | <span data-ttu-id="5a067-186">URL-адрес для открытия в браузере при отладке.</span><span class="sxs-lookup"><span data-stu-id="5a067-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="5a067-187">По умолчанию — `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a067-187">Defaults to `https://localhost:5001`.</span></span> |
| `hosted`  | <span data-ttu-id="5a067-188">Браузер для запуска сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="5a067-188">The browser to launch for the debugging session.</span></span> |
| `webRoot` | <span data-ttu-id="5a067-189">Задайте `edge` или `chrome`.</span><span class="sxs-lookup"><span data-stu-id="5a067-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="5a067-190">По умолчанию — `chrome`.</span><span class="sxs-lookup"><span data-stu-id="5a067-190">Defaults to `chrome`.</span></span> |
| `timeout` | <span data-ttu-id="5a067-191">Используется для создания журналов из отладчика JS.</span><span class="sxs-lookup"><span data-stu-id="5a067-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="5a067-192">Задано значение `true`, чтобы создавались журналы.</span><span class="sxs-lookup"><span data-stu-id="5a067-192">Set to `true` to generate logs.</span></span> |
| `program` | <span data-ttu-id="5a067-193">При запуске и отладке размещенного приложения Blazor WebAssembly необходимо задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="5a067-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> <span data-ttu-id="5a067-194">Указывает полный путь к веб-серверу.</span><span class="sxs-lookup"><span data-stu-id="5a067-194">Specifies the absolute path of the web server.</span></span> |
| `cwd`     | <span data-ttu-id="5a067-195">Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="5a067-195">Should be set if an app is served from a sub-route.</span></span> <span data-ttu-id="5a067-196">Количество миллисекунд ожидания до подключения сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="5a067-196">The number of milliseconds to wait for the debugging session to attach.</span></span> |
| `env`     | <span data-ttu-id="5a067-197">По умолчанию — 30 000 миллисекунд (30 секунд).</span><span class="sxs-lookup"><span data-stu-id="5a067-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> <span data-ttu-id="5a067-198">Ссылка на исполняемый файл для запуска сервера размещенного приложения.</span><span class="sxs-lookup"><span data-stu-id="5a067-198">A reference to the executable to run the server of the hosted app.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="5a067-199">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="5a067-199">Must be set if `hosted` is `true`.</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="5a067-200">Рабочий каталог для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="5a067-200">The working directory to launch the app under.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="5a067-201">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="5a067-201">Must be set if `hosted` is `true`.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="5a067-202">Переменные среды, которые должны быть предоставлены запущенному процессу.</span><span class="sxs-lookup"><span data-stu-id="5a067-202">The environment variables to provide to the launched process.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="5a067-203">Применимо, только если для параметра `hosted` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="5a067-203">Only applicable if `hosted` is set to `true`.</span></span>

1. <span data-ttu-id="5a067-204">Примеры конфигураций запуска</span><span class="sxs-lookup"><span data-stu-id="5a067-204">Example launch configurations</span></span>

1. <span data-ttu-id="5a067-205">Запуск и отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5a067-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

1. <span data-ttu-id="5a067-206">Подключение к выполняющемуся приложению по указанному URL-адресу</span><span class="sxs-lookup"><span data-stu-id="5a067-206">Attach to a running app at a specified URL</span></span> <span data-ttu-id="5a067-207">Запуск и отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5a067-207">Launch and debug a hosted Blazor WebAssembly app</span></span> <span data-ttu-id="5a067-208">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="5a067-208">Debug in the browser</span></span> <span data-ttu-id="5a067-209">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5a067-209">Run a Debug build of the app in the Development environment.</span></span>

<span data-ttu-id="5a067-210">Нажмите клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="5a067-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span> <span data-ttu-id="5a067-211">Браузер должен быть запущен с включенной удаленной отладкой.</span><span class="sxs-lookup"><span data-stu-id="5a067-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="5a067-212">Если удаленная отладка отключена, открывается страница ошибки **Не удается найти вкладку браузера для отладки**.</span><span class="sxs-lookup"><span data-stu-id="5a067-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="5a067-213">На странице ошибки содержатся инструкции для запуска браузера с открытым портом отладки, чтобы прокси-сервер отладки Blazor мог подключиться к приложению.</span><span class="sxs-lookup"><span data-stu-id="5a067-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span>

<span data-ttu-id="5a067-214">*Закройте все экземпляры браузера* и перезапустите браузер.</span><span class="sxs-lookup"><span data-stu-id="5a067-214">*Close all browser instances* and restart the browser as instructed.</span></span> <span data-ttu-id="5a067-215">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш. Через некоторое время на вкладке **Источники** отобразится список сборок .NET в приложении.</span><span class="sxs-lookup"><span data-stu-id="5a067-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="5a067-216">Разверните каждую сборку и найдите исходные файлы *CS*/*RAZOR*, доступные для отладки.</span><span class="sxs-lookup"><span data-stu-id="5a067-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="5a067-217">Установите точки останова, переключитесь обратно на вкладку приложения, и точки останова будут достигнуты при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="5a067-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span>

<span data-ttu-id="5a067-218">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="5a067-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span> Blazor<span data-ttu-id="5a067-219"> предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="5a067-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="5a067-220">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="5a067-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5a067-221">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="5a067-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

<span data-ttu-id="5a067-222">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="5a067-222">Browser source maps</span></span>

* <span data-ttu-id="5a067-223">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="5a067-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="5a067-224">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="5a067-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span>
* <span data-ttu-id="5a067-225">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="5a067-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span> <span data-ttu-id="5a067-226">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="5a067-226">Troubleshoot</span></span>
