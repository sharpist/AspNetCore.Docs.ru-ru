---
<span data-ttu-id="80bf6-101">title: 'Отладка ASP.NET Core Blazor WebAssembly' author: description: 'Узнайте, как отлаживать приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="80bf6-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="80bf6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="80bf6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80bf6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80bf6-103">'Blazor'</span></span>
- <span data-ttu-id="80bf6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80bf6-104">'Identity'</span></span>
- <span data-ttu-id="80bf6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80bf6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="80bf6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80bf6-106">'Razor'</span></span>
- <span data-ttu-id="80bf6-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="80bf6-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="80bf6-108">Отладка ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="80bf6-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="80bf6-109">Дэниэл Рот (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="80bf6-109">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="80bf6-110">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="80bf6-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="80bf6-111">Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="80bf6-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="80bf6-112">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="80bf6-112">Available scenarios include:</span></span>

* <span data-ttu-id="80bf6-113">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="80bf6-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="80bf6-114">Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="80bf6-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="80bf6-115">Выполните код пошагово (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="80bf6-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="80bf6-116">Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="80bf6-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="80bf6-117">На экране *Локальные переменные* просмотрите значения локальных переменных.</span><span class="sxs-lookup"><span data-stu-id="80bf6-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="80bf6-118">См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80bf6-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="80bf6-119">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="80bf6-119">For now, you *can't*:</span></span>

* <span data-ttu-id="80bf6-120">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="80bf6-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="80bf6-121">Попадание в точки останова во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="80bf6-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="80bf6-122">Мы будем улучшать процесс отладки в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="80bf6-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80bf6-123">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="80bf6-123">Prerequisites</span></span>

<span data-ttu-id="80bf6-124">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="80bf6-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="80bf6-125">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="80bf6-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="80bf6-126">Google Chrome (версии 70 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="80bf6-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="80bf6-127">Включение отладки для Visual Studio и Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="80bf6-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="80bf6-128">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл *launchSettings.json* в запускаемом проекте, включив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="80bf6-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="80bf6-129">После изменения файл *launchSettings.json* должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="80bf6-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="80bf6-130">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="80bf6-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="80bf6-131">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="80bf6-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="80bf6-132">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="80bf6-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="80bf6-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80bf6-133">Visual Studio</span></span>

<span data-ttu-id="80bf6-134">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="80bf6-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="80bf6-135">Создайте размещенное приложение Blazor WebAssembly ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80bf6-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="80bf6-136">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="80bf6-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="80bf6-137">Установите точку останова в *Counter.razor* в методе `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="80bf6-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="80bf6-138">Перейдите на вкладку **Счетчик** и нажмите кнопку для попадания в точку останова:</span><span class="sxs-lookup"><span data-stu-id="80bf6-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="80bf6-140">Просмотрите значение поля `currentCount` в окне локальных переменных:</span><span class="sxs-lookup"><span data-stu-id="80bf6-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="80bf6-142">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="80bf6-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="80bf6-143">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:</span><span class="sxs-lookup"><span data-stu-id="80bf6-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="80bf6-144">Установите точку останова на странице *FetchData.razor* в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="80bf6-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="80bf6-145">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="80bf6-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="80bf6-146">Перейдите на вкладку **Получение данных**, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:</span><span class="sxs-lookup"><span data-stu-id="80bf6-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="80bf6-148">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="80bf6-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="80bf6-150">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.</span><span class="sxs-lookup"><span data-stu-id="80bf6-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="80bf6-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="80bf6-151">Visual Studio Code</span></span>

<span data-ttu-id="80bf6-152">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio Code, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="80bf6-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="80bf6-153">Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="80bf6-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Расширения](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Отладчик предварительной версии JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="80bf6-156">Откройте существующее приложение Blazor WebAssembly с включенной отладкой.</span><span class="sxs-lookup"><span data-stu-id="80bf6-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="80bf6-157">Если вы получаете следующее уведомление о том, что для включения отладки требуется дополнительная настройка, убедитесь, что установлены подходящие расширения и Отладка предварительной версии JavaScript включена, а затем перезагрузите окно:</span><span class="sxs-lookup"><span data-stu-id="80bf6-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="80bf6-159">Уведомление предлагает добавить необходимые ресурсы в приложение для сборки и отладки.</span><span class="sxs-lookup"><span data-stu-id="80bf6-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="80bf6-160">Выберите **Да**:</span><span class="sxs-lookup"><span data-stu-id="80bf6-160">Select **Yes**:</span></span>

     ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="80bf6-162">Запуск приложения в отладчике состоит из двух этапов:</span><span class="sxs-lookup"><span data-stu-id="80bf6-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="80bf6-163">1\.</span><span class="sxs-lookup"><span data-stu-id="80bf6-163">1\.</span></span> <span data-ttu-id="80bf6-164">**Сначала** запустите приложение, используя конфигурацию запуска **.NET Core Launch (Blazor Standalone)** (Запуск .NET Core — автономный Blazor).</span><span class="sxs-lookup"><span data-stu-id="80bf6-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="80bf6-165">2\.</span><span class="sxs-lookup"><span data-stu-id="80bf6-165">2\.</span></span> <span data-ttu-id="80bf6-166">**После запуска приложения** запустите браузер, используя конфигурацию запуска **.NET Core Debug Blazor Web Assembly in Chrome** (Blazor Web Assembly отладки .NET Core в Chrome) (требуется Chrome).</span><span class="sxs-lookup"><span data-stu-id="80bf6-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="80bf6-167">Чтобы использовать Edge вместо Chrome, измените `type` конфигурации запуска в *.vscode/launch.json* с `pwa-chrome` на `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="80bf6-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="80bf6-168">Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:</span><span class="sxs-lookup"><span data-stu-id="80bf6-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="80bf6-170">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="80bf6-170">Debug in the browser</span></span>

1. <span data-ttu-id="80bf6-171">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="80bf6-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="80bf6-172">Нажмите клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="80bf6-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="80bf6-173">Браузер должен быть запущен с включенной удаленной отладкой.</span><span class="sxs-lookup"><span data-stu-id="80bf6-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="80bf6-174">Если удаленная отладка отключена, открывается страница ошибки **Не удается найти вкладку браузера для отладки**.</span><span class="sxs-lookup"><span data-stu-id="80bf6-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="80bf6-175">На странице ошибки содержатся инструкции для запуска браузера с открытым портом отладки, чтобы прокси-сервер отладки Blazor мог подключиться к приложению.</span><span class="sxs-lookup"><span data-stu-id="80bf6-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="80bf6-176">*Закройте все экземпляры браузера* и перезапустите браузер.</span><span class="sxs-lookup"><span data-stu-id="80bf6-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="80bf6-177">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш. Через некоторое время на вкладке **Источники** отобразится список сборок .NET в приложении.</span><span class="sxs-lookup"><span data-stu-id="80bf6-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="80bf6-178">Разверните каждую сборку и найдите исходные файлы *CS*/*RAZOR*, доступные для отладки.</span><span class="sxs-lookup"><span data-stu-id="80bf6-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="80bf6-179">Установите точки останова, переключитесь обратно на вкладку приложения, и точки останова будут достигнуты при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="80bf6-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="80bf6-180">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="80bf6-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="80bf6-181"> предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="80bf6-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="80bf6-182">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="80bf6-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="80bf6-183">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="80bf6-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="80bf6-184">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="80bf6-184">Browser source maps</span></span>

<span data-ttu-id="80bf6-185">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="80bf6-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="80bf6-186">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="80bf6-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="80bf6-187">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="80bf6-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="80bf6-188">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="80bf6-188">Troubleshoot</span></span>

<span data-ttu-id="80bf6-189">При возникновении ошибок вам поможет следующий совет:</span><span class="sxs-lookup"><span data-stu-id="80bf6-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="80bf6-190">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="80bf6-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="80bf6-191">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="80bf6-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
