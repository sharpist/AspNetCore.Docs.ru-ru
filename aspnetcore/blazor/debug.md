---
title: 'Отладка в ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: Узнайте, как выполнять отладку приложений :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 669ebaf6dcd05561340aefda4a75b6fe1068d207
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056196"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="de831-103">Отладка в ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="de831-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="de831-104">[Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="de831-104">[Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="de831-105">Приложения :::no-loc(Blazor WebAssembly)::: можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome).</span><span class="sxs-lookup"><span data-stu-id="de831-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="de831-106">Отлаживать приложение можно также с помощью следующих интегрированных сред разработки (IDE).</span><span class="sxs-lookup"><span data-stu-id="de831-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="de831-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de831-107">Visual Studio</span></span>
* <span data-ttu-id="de831-108">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de831-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="de831-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de831-109">Visual Studio Code</span></span>

<span data-ttu-id="de831-110">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="de831-110">Available scenarios include:</span></span>

* <span data-ttu-id="de831-111">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="de831-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="de831-112">Запуск приложения с поддержкой отладки в IDE.</span><span class="sxs-lookup"><span data-stu-id="de831-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="de831-113">Пошаговое прохождение кода.</span><span class="sxs-lookup"><span data-stu-id="de831-113">Single-step through the code.</span></span>
* <span data-ttu-id="de831-114">Возобновление выполнения кода с помощью сочетания клавиш в IDE.</span><span class="sxs-lookup"><span data-stu-id="de831-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="de831-115">Просмотр значений локальных переменных в окне *Локальные переменные*.</span><span class="sxs-lookup"><span data-stu-id="de831-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="de831-116">Просмотр стека вызовов, включая цепочки вызовов между JavaScript и .NET.</span><span class="sxs-lookup"><span data-stu-id="de831-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="de831-117">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="de831-117">For now, you *can't* :</span></span>

* <span data-ttu-id="de831-118">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="de831-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="de831-119">Попадание в точки останова во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="de831-120">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="de831-121">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="de831-121">Prerequisites</span></span>

<span data-ttu-id="de831-122">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="de831-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="de831-123">Google Chrome (версии 70 и более поздних) (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="de831-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="de831-124">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="de831-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="de831-125">Для Visual Studio для Mac требуется версия 8.8 (сборка 1532) или более поздняя.</span><span class="sxs-lookup"><span data-stu-id="de831-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="de831-126">Установите последнюю версию Visual Studio для Mac, нажав кнопку **Скачать Visual Studio для Mac** на странице [Microsoft: Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="de831-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="de831-127">Выберите в Visual Studio канал *Предварительная версия*.</span><span class="sxs-lookup"><span data-stu-id="de831-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="de831-128">Дополнительные сведения см. в статье [Установка предварительной версии Visual Studio для Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="de831-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="de831-129">В настоящее время Apple Safari в macOS не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de831-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="de831-130">Включение отладки</span><span class="sxs-lookup"><span data-stu-id="de831-130">Enable debugging</span></span>

<span data-ttu-id="de831-131">Чтобы включить отладку для существующего приложения :::no-loc(Blazor WebAssembly):::, измените файл `launchSettings.json` в запускаемом проекте, добавив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="de831-131">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="de831-132">После изменения файл `launchSettings.json` должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="de831-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="de831-133">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="de831-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="de831-134">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="de831-134">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="de831-135">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="de831-135">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="de831-136">Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.</span><span class="sxs-lookup"><span data-stu-id="de831-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de831-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de831-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de831-138">Чтобы выполнить отладку приложения :::no-loc(Blazor WebAssembly)::: в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="de831-138">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="de831-139">Создайте новое приложение :::no-loc(Blazor WebAssembly):::, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de831-139">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="de831-140">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="de831-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="de831-141">**Запуск без отладки** ( <kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de831-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="de831-142">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="de831-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="de831-143">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="de831-143">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="de831-144">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me** , чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="de831-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="de831-145">В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.</span><span class="sxs-lookup"><span data-stu-id="de831-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="de831-146">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="de831-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="de831-147">При отладке приложения :::no-loc(Blazor WebAssembly)::: можно также отлаживать серверный код.</span><span class="sxs-lookup"><span data-stu-id="de831-147">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="de831-148">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="de831-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="de831-149">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="de831-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="de831-150">Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.</span><span class="sxs-lookup"><span data-stu-id="de831-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="de831-151">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите в точку останова на сервере в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="de831-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="de831-152">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.</span><span class="sxs-lookup"><span data-stu-id="de831-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="de831-153">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="de831-154">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="de831-155">Если приложение находится по [базовому пути](xref:blazor/host-and-deploy/index#app-base-path), отличному от `/`, измените следующие свойства в `Properties/launchSettings.json` в соответствии с базовым путем приложения:</span><span class="sxs-lookup"><span data-stu-id="de831-155">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="de831-156">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="de831-156">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="de831-157">`inspectUri` каждого профиля:</span><span class="sxs-lookup"><span data-stu-id="de831-157">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="de831-158">Заполнители в указанных выше параметрах:</span><span class="sxs-lookup"><span data-stu-id="de831-158">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="de831-159">`{INSECURE PORT}` — незащищенный порт;</span><span class="sxs-lookup"><span data-stu-id="de831-159">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="de831-160">по умолчанию предоставляется случайное значение, но допускается пользовательский порт;</span><span class="sxs-lookup"><span data-stu-id="de831-160">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="de831-161">`{APP BASE PATH}` — базовый путь приложения;</span><span class="sxs-lookup"><span data-stu-id="de831-161">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="de831-162">`{SECURE PORT}` — защищенный порт;</span><span class="sxs-lookup"><span data-stu-id="de831-162">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="de831-163">по умолчанию предоставляется случайное значение, но допускается пользовательский порт;</span><span class="sxs-lookup"><span data-stu-id="de831-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="de831-164">`{PROFILE 1, 2, ... N}` — профили параметров запуска.</span><span class="sxs-lookup"><span data-stu-id="de831-164">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="de831-165">Как правило, в приложении по умолчанию указывается более одного профиля (например, профиль для IIS Express и профиль проекта, который используется сервером Kestrel).</span><span class="sxs-lookup"><span data-stu-id="de831-165">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="de831-166">В следующих примерах приложение размещается в `/OAT` с базовым путем, настроенным в `wwwroot/index.html` как `<base href="/OAT/">`:</span><span class="sxs-lookup"><span data-stu-id="de831-166">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="de831-167">Сведения об использовании пользовательского базового пути для приложений :::no-loc(Blazor WebAssembly)::: см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="de831-167">For information on using a custom app base path for :::no-loc(Blazor WebAssembly)::: apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de831-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de831-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="de831-169">Отладка автономного приложения :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="de831-169">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span></h2>

1. <span data-ttu-id="de831-170">Откройте автономное приложение :::no-loc(Blazor WebAssembly)::: в VS Code.</span><span class="sxs-lookup"><span data-stu-id="de831-170">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="de831-171">Вы можете получить уведомление о том, что для включения отладки требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="de831-171">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="de831-172">Для отладки приложений :::no-loc(Blazor WebAssembly)::: требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="de831-172">Additional setup is required to debug :::no-loc(Blazor WebAssembly)::: applications.</span></span>

   <span data-ttu-id="de831-173">Если вы получили уведомление:</span><span class="sxs-lookup"><span data-stu-id="de831-173">If you receive the notification:</span></span>

   * <span data-ttu-id="de831-174">Убедитесь, что установлена актуальная версия [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="de831-174">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="de831-175">Чтобы проверить установленные расширения, откройте раздел **Вид** > **Расширения** в строке меню или выберите значок **Расширения** на боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="de831-175">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="de831-176">Убедитесь, что отладчик JavaScript включен.</span><span class="sxs-lookup"><span data-stu-id="de831-176">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="de831-177">Откройте файл параметров в строке меню ( **Файл** > **Настройки** > **Параметры** ).</span><span class="sxs-lookup"><span data-stu-id="de831-177">Open the settings from the menu bar ( **File** > **Preferences** > **Settings** ).</span></span> <span data-ttu-id="de831-178">Выполните поиск по ключевым словам `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="de831-178">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="de831-179">В результатах поиска убедитесь, что флажок **Отладка > JavaScript: Use Preview (JavaScript: использовать предварительную версию)** установлен.</span><span class="sxs-lookup"><span data-stu-id="de831-179">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="de831-180">Если параметр включения отладки предварительной версии отсутствует, обновите VS Code до актуальной версии или установите [расширение отладчика JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code версии 1.46 или более ранней).</span><span class="sxs-lookup"><span data-stu-id="de831-180">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="de831-181">Перезагрузите окно.</span><span class="sxs-lookup"><span data-stu-id="de831-181">Reload the window.</span></span>

1. <span data-ttu-id="de831-182">Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.</span><span class="sxs-lookup"><span data-stu-id="de831-182">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="de831-183">**Запуск без отладки** ( <kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de831-183">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="de831-184">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="de831-184">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="de831-185">При появлении запроса выберите вариант **:::no-loc(Blazor WebAssembly)::: Debug** (Отладка :::no-loc(Blazor WebAssembly):::), чтобы начать отладку.</span><span class="sxs-lookup"><span data-stu-id="de831-185">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

1. <span data-ttu-id="de831-186">Автономное приложение запустится и откроется браузер отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-186">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="de831-187">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="de831-187">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="de831-188">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me** , чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="de831-188">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="de831-189">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-189">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="de831-190">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-190">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="de831-191">Отладка размещенного приложения :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="de831-191">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="de831-192">Откройте папку решения размещенного приложения :::no-loc(Blazor WebAssembly)::: в VS Code.</span><span class="sxs-lookup"><span data-stu-id="de831-192">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="de831-193">Если для проекта не задана конфигурация запуска, появится следующее уведомление.</span><span class="sxs-lookup"><span data-stu-id="de831-193">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="de831-194">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="de831-194">Select **Yes**.</span></span>

   > <span data-ttu-id="de831-195">В {ИМЯ ПРИЛОЖЕНИЯ} отсутствуют необходимые ресурсы для сборки и отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-195">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="de831-196">Добавить их?</span><span class="sxs-lookup"><span data-stu-id="de831-196">Add them?</span></span>

1. <span data-ttu-id="de831-197">В палитре команд вверху окна выберите проект *Server* в размещенном решении.</span><span class="sxs-lookup"><span data-stu-id="de831-197">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="de831-198">При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.</span><span class="sxs-lookup"><span data-stu-id="de831-198">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="de831-199">Подключение к существующему сеансу отладки</span><span class="sxs-lookup"><span data-stu-id="de831-199">Attach to an existing debugging session</span></span>

<span data-ttu-id="de831-200">Чтобы подключиться к выполняющемуся приложению :::no-loc(Blazor):::, создайте файл `launch.json` со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="de831-200">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="de831-201">Подключение к сеансу отладки поддерживается только для автономных приложений.</span><span class="sxs-lookup"><span data-stu-id="de831-201">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="de831-202">Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.</span><span class="sxs-lookup"><span data-stu-id="de831-202">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="de831-203">Параметры конфигурации запуска</span><span class="sxs-lookup"><span data-stu-id="de831-203">Launch configuration options</span></span>

<span data-ttu-id="de831-204">Для отладки типа `blazorwasm` (`.vscode/launch.json`) поддерживаются следующие параметры конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="de831-204">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="de831-205">Параметр</span><span class="sxs-lookup"><span data-stu-id="de831-205">Option</span></span>    | <span data-ttu-id="de831-206">Описание</span><span class="sxs-lookup"><span data-stu-id="de831-206">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="de831-207">Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению :::no-loc(Blazor WebAssembly):::, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению.</span><span class="sxs-lookup"><span data-stu-id="de831-207">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="de831-208">URL-адрес для открытия в браузере при отладке.</span><span class="sxs-lookup"><span data-stu-id="de831-208">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="de831-209">По умолчанию — `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="de831-209">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="de831-210">Браузер для запуска сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-210">The browser to launch for the debugging session.</span></span> <span data-ttu-id="de831-211">Задайте `edge` или `chrome`.</span><span class="sxs-lookup"><span data-stu-id="de831-211">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="de831-212">По умолчанию — `chrome`.</span><span class="sxs-lookup"><span data-stu-id="de831-212">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="de831-213">Используется для создания журналов из отладчика JS.</span><span class="sxs-lookup"><span data-stu-id="de831-213">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="de831-214">Задано значение `true`, чтобы создавались журналы.</span><span class="sxs-lookup"><span data-stu-id="de831-214">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="de831-215">При запуске и отладке размещенного приложения :::no-loc(Blazor WebAssembly)::: необходимо задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="de831-215">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="de831-216">Указывает полный путь к веб-серверу.</span><span class="sxs-lookup"><span data-stu-id="de831-216">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="de831-217">Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="de831-217">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="de831-218">Количество миллисекунд ожидания до подключения сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-218">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="de831-219">По умолчанию — 30 000 миллисекунд (30 секунд).</span><span class="sxs-lookup"><span data-stu-id="de831-219">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="de831-220">Ссылка на исполняемый файл для запуска сервера размещенного приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-220">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="de831-221">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="de831-221">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="de831-222">Рабочий каталог для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-222">The working directory to launch the app under.</span></span> <span data-ttu-id="de831-223">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="de831-223">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="de831-224">Переменные среды, которые должны быть предоставлены запущенному процессу.</span><span class="sxs-lookup"><span data-stu-id="de831-224">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="de831-225">Применимо, только если для параметра `hosted` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="de831-225">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="de831-226">Примеры конфигураций запуска</span><span class="sxs-lookup"><span data-stu-id="de831-226">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="de831-227">Запуск и отладка автономного приложения :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="de831-227">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="de831-228">Подключение к выполняющемуся приложению по указанному URL-адресу</span><span class="sxs-lookup"><span data-stu-id="de831-228">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="de831-229">Запуск и отладка размещенного приложения :::no-loc(Blazor WebAssembly)::: с помощью Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="de831-229">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="de831-230">По умолчанию конфигурация браузера использует Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="de831-230">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="de831-231">При использовании Microsoft Edge для отладки задайте для параметра `browser` значение `edge`.</span><span class="sxs-lookup"><span data-stu-id="de831-231">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="de831-232">Чтобы использовать Google Chrome, не задавайте параметр `browser` или задайте для него значение `chrome`.</span><span class="sxs-lookup"><span data-stu-id="de831-232">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="de831-233">В предыдущем примере `MyHostedApp.Server.dll` является сборкой приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="de831-233">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="de831-234">Папка `.vscode` расположена в папке решения рядом с папками `Client`, `Server` и `Shared`.</span><span class="sxs-lookup"><span data-stu-id="de831-234">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de831-235">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="de831-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="de831-236">Чтобы выполнить отладку приложения :::no-loc(Blazor WebAssembly)::: в Visual Studio для Mac, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="de831-236">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="de831-237">Создайте новое приложение :::no-loc(Blazor WebAssembly):::, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de831-237">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="de831-238">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="de831-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="de831-239">**Запуск без отладки** ( <kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="de831-239">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="de831-240">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="de831-240">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="de831-241">Для сеанса отладки должен быть выбран браузер Google Chrome или Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="de831-241">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="de831-242">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="de831-242">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="de831-243">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me** , чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="de831-243">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="de831-244">В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.</span><span class="sxs-lookup"><span data-stu-id="de831-244">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="de831-245">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="de831-245">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="de831-246">При отладке приложения :::no-loc(Blazor WebAssembly)::: можно также отлаживать серверный код.</span><span class="sxs-lookup"><span data-stu-id="de831-246">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="de831-247">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="de831-247">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="de831-248">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="de831-248">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="de831-249">Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.</span><span class="sxs-lookup"><span data-stu-id="de831-249">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="de831-250">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="de831-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="de831-251">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.</span><span class="sxs-lookup"><span data-stu-id="de831-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="de831-252">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-252">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="de831-253">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-253">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="de831-254">Дополнительные сведения см. в статье [Отладка с помощью Visual Studio для Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="de831-254">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="de831-255">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="de831-255">Debug in the browser</span></span>

<span data-ttu-id="de831-256">*Инструкции в этом разделе относятся к Google Chrome и Microsoft Edge в Windows.*</span><span class="sxs-lookup"><span data-stu-id="de831-256">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="de831-257">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="de831-257">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="de831-258">Запустите браузер и перейдите по URL-адресу приложения (например, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="de831-258">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="de831-259">В браузере попытайтесь запустить удаленную отладку, нажав клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="de831-259">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="de831-260">Браузер должен работать с включенной удаленной отладкой (это не поведение по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="de831-260">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="de831-261">Если удаленная отладка отключена, отобразится страница с ошибкой **Не удается найти вкладку браузера для отладки** с инструкциями по запуску браузера с открытым портом отладки.</span><span class="sxs-lookup"><span data-stu-id="de831-261">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="de831-262">Выполните инструкции в браузере, после чего откроется новое окно браузера.</span><span class="sxs-lookup"><span data-stu-id="de831-262">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="de831-263">Закройте предыдущее окно.</span><span class="sxs-lookup"><span data-stu-id="de831-263">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="de831-264">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш из предыдущего шага.</span><span class="sxs-lookup"><span data-stu-id="de831-264">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="de831-265">Через некоторое время на вкладке **Источники** отобразится список сборок .NET приложения в узле `file://`.</span><span class="sxs-lookup"><span data-stu-id="de831-265">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="de831-266">Точки остановка, заданные в коде компонента (файлы `.razor`) и в файлах с кодом C# (`.cs`), будут срабатывать при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="de831-266">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="de831-267">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="de831-267">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="de831-268">:::no-loc(Blazor)::: предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="de831-268">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="de831-269">При нажатии сочетания клавиш для отладки :::no-loc(Blazor)::: указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="de831-269">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="de831-270">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="de831-270">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="de831-271">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="de831-271">Browser source maps</span></span>

<span data-ttu-id="de831-272">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="de831-272">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="de831-273">Однако :::no-loc(Blazor)::: в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="de831-273">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="de831-274">Вместо этого :::no-loc(Blazor)::: выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="de831-274">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="de831-275">Диагностика</span><span class="sxs-lookup"><span data-stu-id="de831-275">Troubleshoot</span></span>

<span data-ttu-id="de831-276">При возникновении ошибок воспользуйтесь следующими советами:</span><span class="sxs-lookup"><span data-stu-id="de831-276">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="de831-277">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="de831-277">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="de831-278">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="de831-278">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="de831-279">Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de831-279">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="de831-280">Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="de831-280">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="de831-281">В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**.</span><span class="sxs-lookup"><span data-stu-id="de831-281">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="de831-282">Этот параметр включен в Visual Studio по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="de831-282">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="de831-283">Если отладка не работает, проверьте, выбран ли этот параметр.</span><span class="sxs-lookup"><span data-stu-id="de831-283">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="de831-284">Если в среде используется прокси-сервер HTTP, необходимо включить `localhost` в параметры обхода прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="de831-284">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="de831-285">Для этого можно задать переменную среды `NO_PROXY` в одном из двух мест:</span><span class="sxs-lookup"><span data-stu-id="de831-285">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="de831-286">файл `launchSettings.json` проекта;</span><span class="sxs-lookup"><span data-stu-id="de831-286">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="de831-287">на уровне пользовательских или системных переменных среды для применения ко всем приложениям.</span><span class="sxs-lookup"><span data-stu-id="de831-287">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="de831-288">Перезапустите Visual Studio, чтобы изменение переменной среды вступило в силу.</span><span class="sxs-lookup"><span data-stu-id="de831-288">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="de831-289">Точки останова в `OnInitialized{Async}` не срабатывают</span><span class="sxs-lookup"><span data-stu-id="de831-289">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="de831-290">Прокси-сервер платформы :::no-loc(Blazor)::: для отладки запускается не мгновенно, поэтому точки останова в[методе жизненного цикла `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) могут не срабатывать.</span><span class="sxs-lookup"><span data-stu-id="de831-290">The :::no-loc(Blazor)::: framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="de831-291">Мы рекомендуем добавить задержку в начале тела метода, чтобы прокси-сервер отладки успел запуститься до срабатывания точки останова.</span><span class="sxs-lookup"><span data-stu-id="de831-291">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="de831-292">Вы можете включить задержку на основе [директивы компилятора `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), чтобы обеспечить отсутствие задержки для конечной сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="de831-292">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="de831-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="de831-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="de831-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="de831-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="de831-295">истекло время ожидания Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="de831-295">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="de831-296">Если Visual Studio вызывает исключение о том, что не удалось запустить адаптер отладки, указав, что истекло время ожидания, то можно настроить время ожидания с помощью параметра реестра.</span><span class="sxs-lookup"><span data-stu-id="de831-296">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging ":::no-loc(Blazor):::TimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="de831-297">Заполнитель `{TIMEOUT}` в предыдущей команде указывается в миллисекундах.</span><span class="sxs-lookup"><span data-stu-id="de831-297">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="de831-298">Например, одной минуте соответствует значение `60000`.</span><span class="sxs-lookup"><span data-stu-id="de831-298">For example, one minute is assigned as `60000`.</span></span>
