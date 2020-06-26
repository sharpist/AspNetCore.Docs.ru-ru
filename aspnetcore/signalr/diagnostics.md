---
title: Ведение журнала и диагностика в ASP.NET CoreSignalR
author: anurse
description: Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: f2b864d47c98a031872be676a68143bd79f49829
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409102"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="2c471-103">Ведение журнала и диагностика в ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="2c471-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="2c471-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2c471-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="2c471-105">В этой статье приводятся рекомендации по сбору диагностических сведений из SignalR приложения ASP.NET Core для решения проблем.</span><span class="sxs-lookup"><span data-stu-id="2c471-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="2c471-106">Ведение журнала на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="2c471-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="2c471-107">Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="2c471-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2c471-108">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="2c471-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2c471-109">Поскольку SignalR является частью ASP.NET Core, используется система ведения журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c471-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="2c471-110">В конфигурации по умолчанию SignalR записывает в журнал очень мало информации, но это может быть настроено.</span><span class="sxs-lookup"><span data-stu-id="2c471-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="2c471-111">Дополнительные сведения о настройке ведения журнала ASP.NET Core см. в документации по [ASP.NET Core ведению журнала](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="2c471-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="2c471-112">использует две категории регистратора:</span><span class="sxs-lookup"><span data-stu-id="2c471-112"> uses two logger categories:</span></span>

* <span data-ttu-id="2c471-113">`Microsoft.AspNetCore.SignalR`: Для журналов, связанных с протоколами концентраторов, активации концентраторов, вызова методов и других действий, связанных с концентратором.</span><span class="sxs-lookup"><span data-stu-id="2c471-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="2c471-114">`Microsoft.AspNetCore.Http.Connections`: Для журналов, связанных с транспортом, таких как WebSockets, длительный опрос, серверные события и инфраструктура низкого уровня SignalR .</span><span class="sxs-lookup"><span data-stu-id="2c471-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="2c471-115">Чтобы включить подробные журналы из SignalR , настройте оба предыдущих префикса на `Debug` уровень в *appsettings.js* в файле, добавив следующие элементы в `LogLevel` подраздел в `Logging` :</span><span class="sxs-lookup"><span data-stu-id="2c471-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="2c471-116">Это можно также настроить в коде `CreateWebHostBuilder` метода:</span><span class="sxs-lookup"><span data-stu-id="2c471-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="2c471-117">Если конфигурация на основе JSON не используется, задайте следующие значения конфигурации в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2c471-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="2c471-118">Ознакомьтесь с документацией по системе конфигурации, чтобы определить, как указать вложенные значения конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2c471-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="2c471-119">Например, при использовании переменных среды `_` вместо параметра (например,) используются два символа `:` `Logging__LogLevel__Microsoft.AspNetCore.SignalR` .</span><span class="sxs-lookup"><span data-stu-id="2c471-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="2c471-120">Рекомендуется использовать `Debug` уровень при сборе более подробных диагностических сведений о приложении.</span><span class="sxs-lookup"><span data-stu-id="2c471-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="2c471-121">`Trace`Уровень обеспечивает очень низкую диагностику и редко требуется для диагностики проблем в приложении.</span><span class="sxs-lookup"><span data-stu-id="2c471-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="2c471-122">Доступ к журналам на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="2c471-122">Access server-side logs</span></span>

<span data-ttu-id="2c471-123">Доступ к журналам на стороне сервера зависит от среды, в которой выполняется.</span><span class="sxs-lookup"><span data-stu-id="2c471-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="2c471-124">Как консольное приложение за пределами IIS</span><span class="sxs-lookup"><span data-stu-id="2c471-124">As a console app outside IIS</span></span>

<span data-ttu-id="2c471-125">Если вы работаете в консольном приложении, [средство ведения журнала консоли](xref:fundamentals/logging/index#console) должно быть включено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2c471-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="2c471-126">журналы будут отображаться в консоли.</span><span class="sxs-lookup"><span data-stu-id="2c471-126"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="2c471-127">В IIS Express из Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c471-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="2c471-128">Visual Studio отображает выходные данные журнала в окне **вывод** .</span><span class="sxs-lookup"><span data-stu-id="2c471-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="2c471-129">Выберите вариант раскрывающегося списка **веб-сервер ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2c471-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2c471-130">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="2c471-130">Azure App Service</span></span>

<span data-ttu-id="2c471-131">Включите параметр **ведение журнала приложений (FileSystem)** в разделе **журналы диагностики** на портале службы приложений Azure и настройте **уровень** на `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="2c471-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="2c471-132">Журналы должны быть доступны в службе **потоковой передачи журналов** и в журналах файловой системы службы приложений.</span><span class="sxs-lookup"><span data-stu-id="2c471-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="2c471-133">Дополнительные сведения см. в статье [потоковая передача журналов Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="2c471-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="2c471-134">Другие среды</span><span class="sxs-lookup"><span data-stu-id="2c471-134">Other environments</span></span>

<span data-ttu-id="2c471-135">Если приложение развертывается в другой среде (например, Docker, Kubernetes или службе Windows), см. Дополнительные сведения о <xref:fundamentals/logging/index> настройке регистраторов, подходящих для среды.</span><span class="sxs-lookup"><span data-stu-id="2c471-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="2c471-136">Ведение журнала клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="2c471-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2c471-137">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="2c471-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2c471-138">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="2c471-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2c471-139">При использовании клиента JavaScript можно настроить параметры ведения журнала с помощью `configureLogging` метода в `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2c471-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="2c471-140">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="2c471-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2c471-141">В следующей таблице приведены уровни журнала, доступные для клиента JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2c471-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="2c471-142">Установка одного из этих значений уровня ведения журнала позволяет вести журнал на этом уровне и на всех уровнях, напревышающих его в таблице.</span><span class="sxs-lookup"><span data-stu-id="2c471-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="2c471-143">Level</span><span class="sxs-lookup"><span data-stu-id="2c471-143">Level</span></span> | <span data-ttu-id="2c471-144">Описание</span><span class="sxs-lookup"><span data-stu-id="2c471-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="2c471-145">Сообщения не регистрируются.</span><span class="sxs-lookup"><span data-stu-id="2c471-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="2c471-146">Сообщения, указывающие на сбой во всем приложении.</span><span class="sxs-lookup"><span data-stu-id="2c471-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="2c471-147">Сообщения, указывающие на сбой в текущей операции.</span><span class="sxs-lookup"><span data-stu-id="2c471-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="2c471-148">Сообщения, указывающие на некритическую проблему.</span><span class="sxs-lookup"><span data-stu-id="2c471-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="2c471-149">Информационные сообщения.</span><span class="sxs-lookup"><span data-stu-id="2c471-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="2c471-150">Диагностические сообщения, полезные для отладки.</span><span class="sxs-lookup"><span data-stu-id="2c471-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="2c471-151">Очень подробные диагностические сообщения, предназначенные для диагностики конкретных проблем.</span><span class="sxs-lookup"><span data-stu-id="2c471-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="2c471-152">После настройки уровня детализации журналы записываются в консоль браузера (или стандартные выходные данные в приложении NodeJS).</span><span class="sxs-lookup"><span data-stu-id="2c471-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="2c471-153">Если вы хотите отправить журналы в пользовательскую систему ведения журнала, можно предоставить объект JavaScript, реализующий `ILogger` интерфейс.</span><span class="sxs-lookup"><span data-stu-id="2c471-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="2c471-154">Единственным методом, который необходимо реализовать `log` , является, который принимает уровень события и сообщение, связанное с событием.</span><span class="sxs-lookup"><span data-stu-id="2c471-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="2c471-155">Пример.</span><span class="sxs-lookup"><span data-stu-id="2c471-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="2c471-156">Ведение журнала клиента .NET</span><span class="sxs-lookup"><span data-stu-id="2c471-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2c471-157">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="2c471-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2c471-158">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="2c471-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2c471-159">Чтобы получить журналы из клиента .NET, можно использовать `ConfigureLogging` метод для `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2c471-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="2c471-160">Это работает так же, как `ConfigureLogging` и метод в `WebHostBuilder` и `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2c471-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="2c471-161">Вы можете настроить те же поставщики ведения журналов, которые используются в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c471-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="2c471-162">Однако необходимо вручную установить и включить пакеты NuGet для отдельных поставщиков ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="2c471-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="2c471-163">Сведения о добавлении ведения журнала клиента .NET в Blazor WebAssembly приложение см <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> . в разделе.</span><span class="sxs-lookup"><span data-stu-id="2c471-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="2c471-164">Журнал консоли</span><span class="sxs-lookup"><span data-stu-id="2c471-164">Console logging</span></span>

<span data-ttu-id="2c471-165">Чтобы включить ведение журнала консоли, добавьте пакет [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="2c471-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="2c471-166">Затем используйте `AddConsole` метод для настройки средства ведения журнала консоли:</span><span class="sxs-lookup"><span data-stu-id="2c471-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="2c471-167">Ведение журнала окна вывода отладки</span><span class="sxs-lookup"><span data-stu-id="2c471-167">Debug output window logging</span></span>

<span data-ttu-id="2c471-168">Кроме того, можно настроить журналы для перехода в окно **вывода** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2c471-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="2c471-169">Установите пакет [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) и используйте `AddDebug` метод:</span><span class="sxs-lookup"><span data-stu-id="2c471-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="2c471-170">Другие поставщики ведения журнала</span><span class="sxs-lookup"><span data-stu-id="2c471-170">Other logging providers</span></span>

SignalR<span data-ttu-id="2c471-171">поддерживает другие поставщики ведения журналов, такие как Serilog, SEQ, NLog или любая другая система ведения журналов, которая интегрируется с `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="2c471-171"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="2c471-172">Если ваша система ведения журналов предоставляет `ILoggerProvider` , вы можете зарегистрировать ее с помощью `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="2c471-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="2c471-173">Уровень детализации элемента управления</span><span class="sxs-lookup"><span data-stu-id="2c471-173">Control verbosity</span></span>

<span data-ttu-id="2c471-174">При ведении журнала из других мест в приложении изменение уровня по умолчанию на `Debug` может оказаться слишком подробным.</span><span class="sxs-lookup"><span data-stu-id="2c471-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="2c471-175">С помощью фильтра можно настроить уровень ведения журнала для SignalR журналов.</span><span class="sxs-lookup"><span data-stu-id="2c471-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="2c471-176">Это можно сделать в коде во многом так же, как на сервере:</span><span class="sxs-lookup"><span data-stu-id="2c471-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="2c471-177">Данные трассировки сети</span><span class="sxs-lookup"><span data-stu-id="2c471-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="2c471-178">Трассировка сети содержит все содержимое каждого сообщения, отправленного приложением.</span><span class="sxs-lookup"><span data-stu-id="2c471-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="2c471-179">**Никогда не** размещайте необработанные сетевые трассировки из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="2c471-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2c471-180">При возникновении проблемы трассировка сети иногда может предоставить множество полезных сведений.</span><span class="sxs-lookup"><span data-stu-id="2c471-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="2c471-181">Это особенно полезно, если вы собираетесь зафиксировать вопрос в средстве записи проблем.</span><span class="sxs-lookup"><span data-stu-id="2c471-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="2c471-182">Получение трассировки сети с помощью Fiddler (предпочтительный вариант)</span><span class="sxs-lookup"><span data-stu-id="2c471-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="2c471-183">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="2c471-183">This method works for all apps.</span></span>

<span data-ttu-id="2c471-184">Fiddler — это очень мощный инструмент для сбора трассировок HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c471-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="2c471-185">Установите его из [Telerik.com/Fiddler](https://www.telerik.com/fiddler), запустите его, а затем запустите приложение и воспроизведите ошибку.</span><span class="sxs-lookup"><span data-stu-id="2c471-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="2c471-186">Fiddler доступен для Windows, и существуют бета-версии для macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="2c471-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="2c471-187">При подключении по протоколу HTTPS необходимо выполнить некоторые дополнительные действия, чтобы гарантировать, что Fiddler может расшифровать HTTPS трафик.</span><span class="sxs-lookup"><span data-stu-id="2c471-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="2c471-188">Дополнительные сведения см. в [документации по Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c471-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="2c471-189">После сбора трассировки можно экспортировать трассировку, выбрав **файл**  >  **сохранить**  >  **все сеансы** в строке меню.</span><span class="sxs-lookup"><span data-stu-id="2c471-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Экспорт всех сеансов из Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="2c471-191">Получение трассировки сети с помощью tcpdump (только для macOS и Linux)</span><span class="sxs-lookup"><span data-stu-id="2c471-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="2c471-192">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="2c471-192">This method works for all apps.</span></span>

<span data-ttu-id="2c471-193">Вы можете получить необработанные трассировки TCP с помощью tcpdump, выполнив следующую команду в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="2c471-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="2c471-194">Если возникает ошибка разрешений, может потребоваться добавить или добавить к `root` команде префикс `sudo` .</span><span class="sxs-lookup"><span data-stu-id="2c471-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="2c471-195">Замените на `[interface]` сетевой интерфейс, на котором вы хотите захватить.</span><span class="sxs-lookup"><span data-stu-id="2c471-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="2c471-196">Обычно это нечто вроде `/dev/eth0` (для стандартного интерфейса Ethernet) или `/dev/lo0` (для трафика localhost).</span><span class="sxs-lookup"><span data-stu-id="2c471-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="2c471-197">Дополнительные сведения см `tcpdump` . на справочной странице в главной системе.</span><span class="sxs-lookup"><span data-stu-id="2c471-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="2c471-198">Получение трассировки сети в браузере</span><span class="sxs-lookup"><span data-stu-id="2c471-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="2c471-199">Этот метод работает только для приложений на основе браузера.</span><span class="sxs-lookup"><span data-stu-id="2c471-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="2c471-200">Большинство Средства для разработчиков браузера имеют вкладку "сеть", позволяющую записывать сетевые активности между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="2c471-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="2c471-201">Однако эти трассировки не включают WebSocket и сообщения, отправляемые сервером.</span><span class="sxs-lookup"><span data-stu-id="2c471-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="2c471-202">Если вы используете эти транспорты, лучше подходить с помощью такого средства, как Fiddler или TcpDump (описанное ниже).</span><span class="sxs-lookup"><span data-stu-id="2c471-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="2c471-203">Microsoft Edge и Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="2c471-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="2c471-204">(Инструкции одинаковы для обеих и Internet Explorer).</span><span class="sxs-lookup"><span data-stu-id="2c471-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="2c471-205">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="2c471-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2c471-206">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="2c471-206">Click the Network Tab</span></span>
3. <span data-ttu-id="2c471-207">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="2c471-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2c471-208">Щелкните значок "Сохранить" на панели инструментов, чтобы экспортировать трассировку в виде файла "HAR":</span><span class="sxs-lookup"><span data-stu-id="2c471-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Значок "Сохранить" на вкладке "сеть" средств разработки Microsoft ребра](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="2c471-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="2c471-210">Google Chrome</span></span>

1. <span data-ttu-id="2c471-211">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="2c471-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2c471-212">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="2c471-212">Click the Network Tab</span></span>
3. <span data-ttu-id="2c471-213">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="2c471-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2c471-214">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите команду "Сохранить как HAR с содержимым":</span><span class="sxs-lookup"><span data-stu-id="2c471-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Параметр "Сохранить как HAR с содержимым" на вкладке "сеть" средств разработки Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="2c471-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2c471-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="2c471-217">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="2c471-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2c471-218">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="2c471-218">Click the Network Tab</span></span>
3. <span data-ttu-id="2c471-219">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="2c471-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2c471-220">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите "сохранить все как HAR".</span><span class="sxs-lookup"><span data-stu-id="2c471-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Параметр "сохранить все как HAR" на вкладке "сеть" средств разработки Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="2c471-222">Присоединение файлов диагностики к проблемам GitHub</span><span class="sxs-lookup"><span data-stu-id="2c471-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="2c471-223">Вы можете присоединить диагностические файлы к проблемам GitHub, переименовав их, чтобы они имели `.txt` расширение, а затем перетащив их на ошибку.</span><span class="sxs-lookup"><span data-stu-id="2c471-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="2c471-224">Не вставляйте содержимое файлов журнала или трассировки сети в проблемы GitHub.</span><span class="sxs-lookup"><span data-stu-id="2c471-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="2c471-225">Эти журналы и трассировки могут быть довольно большими, и GitHub обычно усекает их.</span><span class="sxs-lookup"><span data-stu-id="2c471-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Перетаскивание файлов журнала на вопрос GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="2c471-227">Метрики</span><span class="sxs-lookup"><span data-stu-id="2c471-227">Metrics</span></span>

<span data-ttu-id="2c471-228">Метрики — это представление мер данных за интервалы времени.</span><span class="sxs-lookup"><span data-stu-id="2c471-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="2c471-229">Например, количество запросов в секунду.</span><span class="sxs-lookup"><span data-stu-id="2c471-229">For example, requests per second.</span></span> <span data-ttu-id="2c471-230">Данные метрик позволяют выполнять наблюдение за состоянием приложения на высоком уровне.</span><span class="sxs-lookup"><span data-stu-id="2c471-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="2c471-231">Метрики .NET gRPC создаются с помощью <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="2c471-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="signalr-server-metrics"></a>SignalR<span data-ttu-id="2c471-232">метрики сервера</span><span class="sxs-lookup"><span data-stu-id="2c471-232"> server metrics</span></span>

SignalR<span data-ttu-id="2c471-233">метрики сервера указываются в <xref:Microsoft.AspNetCore.Http.Connections> источнике событий.</span><span class="sxs-lookup"><span data-stu-id="2c471-233"> server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="2c471-234">name</span><span class="sxs-lookup"><span data-stu-id="2c471-234">Name</span></span>                    | <span data-ttu-id="2c471-235">Описание:</span><span class="sxs-lookup"><span data-stu-id="2c471-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="2c471-236">Всего запущенных подключений</span><span class="sxs-lookup"><span data-stu-id="2c471-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="2c471-237">Всего остановленных подключений</span><span class="sxs-lookup"><span data-stu-id="2c471-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="2c471-238">Всего подключений истекло</span><span class="sxs-lookup"><span data-stu-id="2c471-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="2c471-239">Текущие подключения</span><span class="sxs-lookup"><span data-stu-id="2c471-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="2c471-240">Средняя продолжительность соединения</span><span class="sxs-lookup"><span data-stu-id="2c471-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="2c471-241">Наблюдение за метриками</span><span class="sxs-lookup"><span data-stu-id="2c471-241">Observe metrics</span></span>

<span data-ttu-id="2c471-242">[DotNet-Counters](/dotnet/core/diagnostics/dotnet-counters) — это средство мониторинга производительности для нерегламентированного мониторинга работоспособности и анализа производительности первого уровня.</span><span class="sxs-lookup"><span data-stu-id="2c471-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="2c471-243">Мониторинг приложения .NET с помощью в `Microsoft.AspNetCore.Http.Connections` качестве имени поставщика.</span><span class="sxs-lookup"><span data-stu-id="2c471-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="2c471-244">Например:</span><span class="sxs-lookup"><span data-stu-id="2c471-244">For example:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2c471-245">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2c471-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
