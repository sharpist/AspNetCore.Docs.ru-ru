---
title: Ведение журнала и диагностика в ASP.NET Core SignalR
author: anurse
description: Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 649398a3868117b2e7f3358aa25544c99cc625b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631346"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="08d30-103">Ведение журнала и диагностика в ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="08d30-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="08d30-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="08d30-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="08d30-105">В этой статье приводятся рекомендации по сбору диагностических сведений из SignalR приложения ASP.NET Core для решения проблем.</span><span class="sxs-lookup"><span data-stu-id="08d30-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="08d30-106">Ведение журнала на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="08d30-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="08d30-107">Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="08d30-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="08d30-108">**Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.</span><span class="sxs-lookup"><span data-stu-id="08d30-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="08d30-109">Поскольку SignalR является частью ASP.NET Core, используется система ведения журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08d30-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="08d30-110">В конфигурации по умолчанию SignalR записывает в журнал очень мало информации, но это может быть настроено.</span><span class="sxs-lookup"><span data-stu-id="08d30-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="08d30-111">Подробные сведения о настройке ведения журналов в ASP.NET Core см. в документации по [ведению журналов в ASP.NET Core](xref:fundamentals/logging/index#configuration).</span><span class="sxs-lookup"><span data-stu-id="08d30-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="08d30-112">SignalR использует две категории регистратора:</span><span class="sxs-lookup"><span data-stu-id="08d30-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="08d30-113">`Microsoft.AspNetCore.SignalR`: Для журналов, связанных с протоколами концентраторов, активации концентраторов, вызова методов и других действий, связанных с концентратором.</span><span class="sxs-lookup"><span data-stu-id="08d30-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="08d30-114">`Microsoft.AspNetCore.Http.Connections`: Для журналов, связанных с транспортом, таких как WebSockets, длительный опрос, серверные события и инфраструктура низкого уровня SignalR .</span><span class="sxs-lookup"><span data-stu-id="08d30-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="08d30-115">Чтобы включить подробные журналы из SignalR , настройте оба предыдущих префикса на `Debug` уровень в *appsettings.js* в файле, добавив следующие элементы в `LogLevel` подраздел в `Logging` :</span><span class="sxs-lookup"><span data-stu-id="08d30-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="08d30-116">Это можно также настроить в коде `CreateWebHostBuilder` метода:</span><span class="sxs-lookup"><span data-stu-id="08d30-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="08d30-117">Если конфигурация на основе JSON не используется, задайте следующие значения конфигурации в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="08d30-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="08d30-118">Сведения о задании вложенных значений конфигурации см. в документации по своей системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="08d30-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="08d30-119">Так, при использовании переменных среды вместо символа `:` применяются два символа `_` (например, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="08d30-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="08d30-120">При сборе подробных диагностических данных для приложения мы рекомендуем использовать уровень `Debug`.</span><span class="sxs-lookup"><span data-stu-id="08d30-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="08d30-121">На уровне `Trace` создаются очень подробные диагностические данные, которые редко требуются для диагностики проблем в приложении.</span><span class="sxs-lookup"><span data-stu-id="08d30-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="08d30-122">Доступ к журналам на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="08d30-122">Access server-side logs</span></span>

<span data-ttu-id="08d30-123">Способ доступа к журналам на стороне сервера зависит от среды, в которой выполняется приложение.</span><span class="sxs-lookup"><span data-stu-id="08d30-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="08d30-124">Как консольное приложение за пределами IIS</span><span class="sxs-lookup"><span data-stu-id="08d30-124">As a console app outside IIS</span></span>

<span data-ttu-id="08d30-125">В консольном приложении по умолчанию должно быть включено [средство ведения журнала консоли](xref:fundamentals/logging/index#console).</span><span class="sxs-lookup"><span data-stu-id="08d30-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="08d30-126">SignalR журналы будут отображаться в консоли.</span><span class="sxs-lookup"><span data-stu-id="08d30-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="08d30-127">В IIS Express из Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08d30-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="08d30-128">Visual Studio отображает выходные данные журнала в окне **вывод** .</span><span class="sxs-lookup"><span data-stu-id="08d30-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="08d30-129">Выберите вариант раскрывающегося списка **веб-сервер ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="08d30-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="08d30-130">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="08d30-130">Azure App Service</span></span>

<span data-ttu-id="08d30-131">Включите параметр **ведение журнала приложений (FileSystem)** в разделе **журналы диагностики** на портале службы приложений Azure и настройте **уровень** на `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="08d30-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="08d30-132">Журналы должны быть доступны в службе **потоковой передачи журналов** и в журналах файловой системы службы приложений.</span><span class="sxs-lookup"><span data-stu-id="08d30-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="08d30-133">Дополнительные сведения см. в статье [потоковая передача журналов Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="08d30-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="08d30-134">Другие среды</span><span class="sxs-lookup"><span data-stu-id="08d30-134">Other environments</span></span>

<span data-ttu-id="08d30-135">Если приложение развернуто в другой среде (например, Docker, Kubernetes или службе Windows), обратитесь к статье <xref:fundamentals/logging/index> за дополнительными сведениями о настройке подходящих поставщиков ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="08d30-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="08d30-136">Ведение журнала клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="08d30-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="08d30-137">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="08d30-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="08d30-138">**Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.</span><span class="sxs-lookup"><span data-stu-id="08d30-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="08d30-139">При использовании клиента JavaScript можно настроить параметры ведения журнала с помощью `configureLogging` метода в `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="08d30-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="08d30-140">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="08d30-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="08d30-141">В следующей таблице приведены уровни журнала, доступные для клиента JavaScript.</span><span class="sxs-lookup"><span data-stu-id="08d30-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="08d30-142">Установка одного из этих значений уровня ведения журнала позволяет вести журнал на этом уровне и на всех уровнях, напревышающих его в таблице.</span><span class="sxs-lookup"><span data-stu-id="08d30-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="08d30-143">Level</span><span class="sxs-lookup"><span data-stu-id="08d30-143">Level</span></span> | <span data-ttu-id="08d30-144">Описание</span><span class="sxs-lookup"><span data-stu-id="08d30-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="08d30-145">Сообщения не регистрируются.</span><span class="sxs-lookup"><span data-stu-id="08d30-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="08d30-146">Сообщения, указывающие на сбой во всем приложении.</span><span class="sxs-lookup"><span data-stu-id="08d30-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="08d30-147">Сообщения, указывающие на сбой в текущей операции.</span><span class="sxs-lookup"><span data-stu-id="08d30-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="08d30-148">Сообщения, указывающие на некритическую проблему.</span><span class="sxs-lookup"><span data-stu-id="08d30-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="08d30-149">Информационные сообщения.</span><span class="sxs-lookup"><span data-stu-id="08d30-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="08d30-150">Диагностические сообщения, полезные для отладки.</span><span class="sxs-lookup"><span data-stu-id="08d30-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="08d30-151">Очень подробные диагностические сообщения, предназначенные для диагностики конкретных проблем.</span><span class="sxs-lookup"><span data-stu-id="08d30-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="08d30-152">После настройки уровня детализации журналы записываются в консоль браузера (или стандартные выходные данные в приложении NodeJS).</span><span class="sxs-lookup"><span data-stu-id="08d30-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="08d30-153">Если вы хотите отправить журналы в пользовательскую систему ведения журнала, можно предоставить объект JavaScript, реализующий `ILogger` интерфейс.</span><span class="sxs-lookup"><span data-stu-id="08d30-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="08d30-154">Единственным методом, который необходимо реализовать `log` , является, который принимает уровень события и сообщение, связанное с событием.</span><span class="sxs-lookup"><span data-stu-id="08d30-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="08d30-155">Пример:</span><span class="sxs-lookup"><span data-stu-id="08d30-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="08d30-156">Ведение журнала клиента  .NET</span><span class="sxs-lookup"><span data-stu-id="08d30-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="08d30-157">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="08d30-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="08d30-158">**Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.</span><span class="sxs-lookup"><span data-stu-id="08d30-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="08d30-159">Чтобы получить журналы из клиента .NET, можно использовать `ConfigureLogging` метод для `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="08d30-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="08d30-160">Это работает так же, как `ConfigureLogging` и метод в `WebHostBuilder` и `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="08d30-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="08d30-161">Вы можете настроить те же поставщики ведения журналов, которые используются в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08d30-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="08d30-162">Однако необходимо вручную установить и включить пакеты NuGet для отдельных поставщиков ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="08d30-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="08d30-163">Сведения о добавлении ведения журнала клиента .NET в Blazor WebAssembly приложение см <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> . в разделе.</span><span class="sxs-lookup"><span data-stu-id="08d30-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="08d30-164">Журнал консоли</span><span class="sxs-lookup"><span data-stu-id="08d30-164">Console logging</span></span>

<span data-ttu-id="08d30-165">Чтобы включить ведение журнала консоли, добавьте пакет [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="08d30-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="08d30-166">Затем используйте `AddConsole` метод для настройки средства ведения журнала консоли:</span><span class="sxs-lookup"><span data-stu-id="08d30-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="08d30-167">Ведение журнала окна вывода отладки</span><span class="sxs-lookup"><span data-stu-id="08d30-167">Debug output window logging</span></span>

<span data-ttu-id="08d30-168">Кроме того, можно настроить журналы для перехода в окно **вывода** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="08d30-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="08d30-169">Установите пакет [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) и используйте `AddDebug` метод:</span><span class="sxs-lookup"><span data-stu-id="08d30-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="08d30-170">Другие поставщики ведения журнала</span><span class="sxs-lookup"><span data-stu-id="08d30-170">Other logging providers</span></span>

<span data-ttu-id="08d30-171">SignalR поддерживает другие поставщики ведения журналов, такие как Serilog, SEQ, NLog или любая другая система ведения журналов, которая интегрируется с `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="08d30-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="08d30-172">Если ваша система ведения журналов предоставляет `ILoggerProvider` , вы можете зарегистрировать ее с помощью `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="08d30-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="08d30-173">Уровень детализации элемента управления</span><span class="sxs-lookup"><span data-stu-id="08d30-173">Control verbosity</span></span>

<span data-ttu-id="08d30-174">При ведении журнала из других мест в приложении изменение уровня по умолчанию на `Debug` может оказаться слишком подробным.</span><span class="sxs-lookup"><span data-stu-id="08d30-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="08d30-175">С помощью фильтра можно настроить уровень ведения журнала для SignalR журналов.</span><span class="sxs-lookup"><span data-stu-id="08d30-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="08d30-176">Это можно сделать в коде во многом так же, как на сервере:</span><span class="sxs-lookup"><span data-stu-id="08d30-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="08d30-177">Данные трассировки сети</span><span class="sxs-lookup"><span data-stu-id="08d30-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="08d30-178">Трассировка сети содержит все содержимое каждого сообщения, отправленного приложением.</span><span class="sxs-lookup"><span data-stu-id="08d30-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="08d30-179">**Никогда не** размещайте необработанные сетевые трассировки из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="08d30-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="08d30-180">При возникновении проблемы трассировка сети иногда может предоставить множество полезных сведений.</span><span class="sxs-lookup"><span data-stu-id="08d30-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="08d30-181">Это особенно полезно, если вы собираетесь зафиксировать вопрос в средстве записи проблем.</span><span class="sxs-lookup"><span data-stu-id="08d30-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="08d30-182">Получение трассировки сети с помощью Fiddler (предпочтительный вариант)</span><span class="sxs-lookup"><span data-stu-id="08d30-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="08d30-183">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="08d30-183">This method works for all apps.</span></span>

<span data-ttu-id="08d30-184">Fiddler — это очень мощный инструмент для сбора трассировок HTTP.</span><span class="sxs-lookup"><span data-stu-id="08d30-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="08d30-185">Установите его из [Telerik.com/Fiddler](https://www.telerik.com/fiddler), запустите его, а затем запустите приложение и воспроизведите ошибку.</span><span class="sxs-lookup"><span data-stu-id="08d30-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="08d30-186">Fiddler доступен для Windows, и существуют бета-версии для macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="08d30-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="08d30-187">При подключении по протоколу HTTPS необходимо выполнить некоторые дополнительные действия, чтобы гарантировать, что Fiddler может расшифровать HTTPS трафик.</span><span class="sxs-lookup"><span data-stu-id="08d30-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="08d30-188">Дополнительные сведения см. в [документации по Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="08d30-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="08d30-189">После сбора трассировки можно экспортировать трассировку, выбрав **файл**  >  **сохранить**  >  **все сеансы** в строке меню.</span><span class="sxs-lookup"><span data-stu-id="08d30-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Экспорт всех сеансов из Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="08d30-191">Получение трассировки сети с помощью tcpdump (только для macOS и Linux)</span><span class="sxs-lookup"><span data-stu-id="08d30-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="08d30-192">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="08d30-192">This method works for all apps.</span></span>

<span data-ttu-id="08d30-193">Вы можете получить необработанные трассировки TCP с помощью tcpdump, выполнив следующую команду в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="08d30-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="08d30-194">Если возникает ошибка разрешений, может потребоваться добавить или добавить к `root` команде префикс `sudo` .</span><span class="sxs-lookup"><span data-stu-id="08d30-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="08d30-195">Замените на `[interface]` сетевой интерфейс, на котором вы хотите захватить.</span><span class="sxs-lookup"><span data-stu-id="08d30-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="08d30-196">Обычно это нечто вроде `/dev/eth0` (для стандартного интерфейса Ethernet) или `/dev/lo0` (для трафика localhost).</span><span class="sxs-lookup"><span data-stu-id="08d30-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="08d30-197">Дополнительные сведения см `tcpdump` . на справочной странице в главной системе.</span><span class="sxs-lookup"><span data-stu-id="08d30-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="08d30-198">Получение трассировки сети в браузере</span><span class="sxs-lookup"><span data-stu-id="08d30-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="08d30-199">Этот метод работает только для приложений на основе браузера.</span><span class="sxs-lookup"><span data-stu-id="08d30-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="08d30-200">Большинство Средства для разработчиков браузера имеют вкладку "сеть", позволяющую записывать сетевые активности между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="08d30-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="08d30-201">Однако эти трассировки не включают WebSocket и сообщения, отправляемые сервером.</span><span class="sxs-lookup"><span data-stu-id="08d30-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="08d30-202">Если вы используете эти транспорты, лучше подходить с помощью такого средства, как Fiddler или TcpDump (описанное ниже).</span><span class="sxs-lookup"><span data-stu-id="08d30-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="08d30-203">Microsoft Edge и Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="08d30-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="08d30-204">(Инструкции одинаковы для обеих и Internet Explorer).</span><span class="sxs-lookup"><span data-stu-id="08d30-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="08d30-205">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="08d30-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="08d30-206">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="08d30-206">Click the Network Tab</span></span>
3. <span data-ttu-id="08d30-207">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="08d30-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="08d30-208">Щелкните значок "Сохранить" на панели инструментов, чтобы экспортировать трассировку в виде файла "HAR":</span><span class="sxs-lookup"><span data-stu-id="08d30-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Значок "Сохранить" на вкладке "сеть" средств разработки Microsoft ребра](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="08d30-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="08d30-210">Google Chrome</span></span>

1. <span data-ttu-id="08d30-211">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="08d30-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="08d30-212">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="08d30-212">Click the Network Tab</span></span>
3. <span data-ttu-id="08d30-213">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="08d30-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="08d30-214">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите команду "Сохранить как HAR с содержимым":</span><span class="sxs-lookup"><span data-stu-id="08d30-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Параметр "Сохранить как HAR с содержимым" на вкладке "сеть" средств разработки Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="08d30-216">Mozilla Firefox;</span><span class="sxs-lookup"><span data-stu-id="08d30-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="08d30-217">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="08d30-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="08d30-218">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="08d30-218">Click the Network Tab</span></span>
3. <span data-ttu-id="08d30-219">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="08d30-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="08d30-220">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите "сохранить все как HAR".</span><span class="sxs-lookup"><span data-stu-id="08d30-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Параметр "сохранить все как HAR" на вкладке "сеть" средств разработки Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="08d30-222">Присоединение файлов диагностики к проблемам GitHub</span><span class="sxs-lookup"><span data-stu-id="08d30-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="08d30-223">Вы можете присоединить диагностические файлы к проблемам GitHub, переименовав их, чтобы они имели `.txt` расширение, а затем перетащив их на ошибку.</span><span class="sxs-lookup"><span data-stu-id="08d30-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="08d30-224">Не вставляйте содержимое файлов журнала или трассировки сети в проблемы GitHub.</span><span class="sxs-lookup"><span data-stu-id="08d30-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="08d30-225">Эти журналы и трассировки могут быть довольно большими, и GitHub обычно усекает их.</span><span class="sxs-lookup"><span data-stu-id="08d30-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Перетаскивание файлов журнала на вопрос GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="08d30-227">Метрики</span><span class="sxs-lookup"><span data-stu-id="08d30-227">Metrics</span></span>

<span data-ttu-id="08d30-228">Метрики — это представление мер данных за интервалы времени.</span><span class="sxs-lookup"><span data-stu-id="08d30-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="08d30-229">Например, количество запросов в секунду.</span><span class="sxs-lookup"><span data-stu-id="08d30-229">For example, requests per second.</span></span> <span data-ttu-id="08d30-230">Данные метрик позволяют выполнять наблюдение за состоянием приложения на высоком уровне.</span><span class="sxs-lookup"><span data-stu-id="08d30-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="08d30-231">Метрики .NET gRPC создаются с помощью <xref:System.Diagnostics.Tracing.EventCounter>.</span><span class="sxs-lookup"><span data-stu-id="08d30-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="08d30-232">SignalR метрики сервера</span><span class="sxs-lookup"><span data-stu-id="08d30-232">SignalR server metrics</span></span>

<span data-ttu-id="08d30-233">SignalR метрики сервера указываются в <xref:Microsoft.AspNetCore.Http.Connections> источнике событий.</span><span class="sxs-lookup"><span data-stu-id="08d30-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="08d30-234">Имя</span><span class="sxs-lookup"><span data-stu-id="08d30-234">Name</span></span>                    | <span data-ttu-id="08d30-235">Описание</span><span class="sxs-lookup"><span data-stu-id="08d30-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="08d30-236">Всего запущенных подключений</span><span class="sxs-lookup"><span data-stu-id="08d30-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="08d30-237">Всего остановленных подключений</span><span class="sxs-lookup"><span data-stu-id="08d30-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="08d30-238">Всего подключений истекло</span><span class="sxs-lookup"><span data-stu-id="08d30-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="08d30-239">Текущие подключения</span><span class="sxs-lookup"><span data-stu-id="08d30-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="08d30-240">Средняя продолжительность соединения</span><span class="sxs-lookup"><span data-stu-id="08d30-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="08d30-241">Отслеживание метрик</span><span class="sxs-lookup"><span data-stu-id="08d30-241">Observe metrics</span></span>

<span data-ttu-id="08d30-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) — это средство мониторинга производительности и первого уровня анализа производительности.</span><span class="sxs-lookup"><span data-stu-id="08d30-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="08d30-243">Мониторинг приложения .NET с помощью в `Microsoft.AspNetCore.Http.Connections` качестве имени поставщика.</span><span class="sxs-lookup"><span data-stu-id="08d30-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="08d30-244">Например:</span><span class="sxs-lookup"><span data-stu-id="08d30-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="08d30-245">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="08d30-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
