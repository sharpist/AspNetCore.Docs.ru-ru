---
<span data-ttu-id="658c1-101">Title: "ведение журнала и диагностика в ASP.NET Core SignalR " Автор: описание: "Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="658c1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="658c1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="658c1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="658c1-103">'Blazor'</span></span>
- <span data-ttu-id="658c1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="658c1-104">'Identity'</span></span>
- <span data-ttu-id="658c1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="658c1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="658c1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="658c1-106">'Razor'</span></span>
- <span data-ttu-id="658c1-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="658c1-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="658c1-108">Ведение журнала и диагностика в ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="658c1-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="658c1-109">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="658c1-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="658c1-110">В этой статье приводятся рекомендации по сбору диагностических сведений из SignalR приложения ASP.NET Core для решения проблем.</span><span class="sxs-lookup"><span data-stu-id="658c1-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="658c1-111">Ведение журнала на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="658c1-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="658c1-112">Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="658c1-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="658c1-113">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="658c1-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="658c1-114">Поскольку SignalR является частью ASP.NET Core, используется система ведения журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="658c1-115">В конфигурации по умолчанию SignalR записывает в журнал очень мало информации, но это может быть настроено.</span><span class="sxs-lookup"><span data-stu-id="658c1-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="658c1-116">Дополнительные сведения о настройке ведения журнала ASP.NET Core см. в документации по [ASP.NET Core ведению журнала](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="658c1-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="658c1-117">использует две категории регистратора:</span><span class="sxs-lookup"><span data-stu-id="658c1-117"> uses two logger categories:</span></span>

* <span data-ttu-id="658c1-118">`Microsoft.AspNetCore.SignalR`: Для журналов, связанных с протоколами концентраторов, активации концентраторов, вызова методов и других действий, связанных с концентратором.</span><span class="sxs-lookup"><span data-stu-id="658c1-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="658c1-119">`Microsoft.AspNetCore.Http.Connections`: Для журналов, связанных с транспортом, таких как WebSockets, длительный опрос, серверные события и инфраструктура низкого уровня SignalR .</span><span class="sxs-lookup"><span data-stu-id="658c1-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="658c1-120">Чтобы включить подробные журналы из SignalR , настройте оба предыдущих префикса на `Debug` уровень в файле *appSettings. JSON* , добавив следующие элементы в `LogLevel` подраздел в `Logging` :</span><span class="sxs-lookup"><span data-stu-id="658c1-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="658c1-121">Это можно также настроить в коде `CreateWebHostBuilder` метода:</span><span class="sxs-lookup"><span data-stu-id="658c1-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="658c1-122">Если конфигурация на основе JSON не используется, задайте следующие значения конфигурации в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="658c1-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="658c1-123">Ознакомьтесь с документацией по системе конфигурации, чтобы определить, как указать вложенные значения конфигурации.</span><span class="sxs-lookup"><span data-stu-id="658c1-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="658c1-124">Например, при использовании переменных среды `_` вместо параметра (например,) используются два символа `:` `Logging__LogLevel__Microsoft.AspNetCore.SignalR` .</span><span class="sxs-lookup"><span data-stu-id="658c1-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="658c1-125">Рекомендуется использовать `Debug` уровень при сборе более подробных диагностических сведений о приложении.</span><span class="sxs-lookup"><span data-stu-id="658c1-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="658c1-126">`Trace`Уровень обеспечивает очень низкую диагностику и редко требуется для диагностики проблем в приложении.</span><span class="sxs-lookup"><span data-stu-id="658c1-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="658c1-127">Доступ к журналам на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="658c1-127">Access server-side logs</span></span>

<span data-ttu-id="658c1-128">Доступ к журналам на стороне сервера зависит от среды, в которой выполняется.</span><span class="sxs-lookup"><span data-stu-id="658c1-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="658c1-129">Как консольное приложение за пределами IIS</span><span class="sxs-lookup"><span data-stu-id="658c1-129">As a console app outside IIS</span></span>

<span data-ttu-id="658c1-130">Если вы работаете в консольном приложении, [средство ведения журнала консоли](xref:fundamentals/logging/index#console) должно быть включено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="658c1-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="658c1-131">журналы будут отображаться в консоли.</span><span class="sxs-lookup"><span data-stu-id="658c1-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="658c1-132">В IIS Express из Visual Studio</span><span class="sxs-lookup"><span data-stu-id="658c1-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="658c1-133">Visual Studio отображает выходные данные журнала в окне **вывод** .</span><span class="sxs-lookup"><span data-stu-id="658c1-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="658c1-134">Выберите вариант раскрывающегося списка **веб-сервер ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="658c1-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="658c1-135">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="658c1-135">Azure App Service</span></span>

<span data-ttu-id="658c1-136">Включите параметр **ведение журнала приложений (FileSystem)** в разделе **журналы диагностики** на портале службы приложений Azure и настройте **уровень** на `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="658c1-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="658c1-137">Журналы должны быть доступны в службе **потоковой передачи журналов** и в журналах файловой системы службы приложений.</span><span class="sxs-lookup"><span data-stu-id="658c1-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="658c1-138">Дополнительные сведения см. в статье [потоковая передача журналов Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="658c1-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="658c1-139">Другие среды</span><span class="sxs-lookup"><span data-stu-id="658c1-139">Other environments</span></span>

<span data-ttu-id="658c1-140">Если приложение развертывается в другой среде (например, Docker, Kubernetes или службе Windows), см. Дополнительные сведения о <xref:fundamentals/logging/index> настройке регистраторов, подходящих для среды.</span><span class="sxs-lookup"><span data-stu-id="658c1-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="658c1-141">Ведение журнала клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="658c1-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="658c1-142">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="658c1-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="658c1-143">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="658c1-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="658c1-144">При использовании клиента JavaScript можно настроить параметры ведения журнала с помощью `configureLogging` метода в `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="658c1-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="658c1-145">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="658c1-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="658c1-146">В следующей таблице приведены уровни журнала, доступные для клиента JavaScript.</span><span class="sxs-lookup"><span data-stu-id="658c1-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="658c1-147">Установка одного из этих значений уровня ведения журнала позволяет вести журнал на этом уровне и на всех уровнях, напревышающих его в таблице.</span><span class="sxs-lookup"><span data-stu-id="658c1-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="658c1-148">Level</span><span class="sxs-lookup"><span data-stu-id="658c1-148">Level</span></span> | <span data-ttu-id="658c1-149">Описание</span><span class="sxs-lookup"><span data-stu-id="658c1-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="658c1-150">Title: "ведение журнала и диагностика в ASP.NET Core SignalR " Автор: описание: "Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="658c1-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="658c1-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="658c1-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="658c1-152">'Blazor'</span></span>
- <span data-ttu-id="658c1-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="658c1-153">'Identity'</span></span>
- <span data-ttu-id="658c1-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="658c1-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="658c1-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="658c1-155">'Razor'</span></span>
- <span data-ttu-id="658c1-156">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="658c1-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="658c1-157">Title: "ведение журнала и диагностика в ASP.NET Core SignalR " Автор: описание: "Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="658c1-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="658c1-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="658c1-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="658c1-159">'Blazor'</span></span>
- <span data-ttu-id="658c1-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="658c1-160">'Identity'</span></span>
- <span data-ttu-id="658c1-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="658c1-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="658c1-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="658c1-162">'Razor'</span></span>
- <span data-ttu-id="658c1-163">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="658c1-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="658c1-164">Title: "ведение журнала и диагностика в ASP.NET Core SignalR " Автор: описание: "Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="658c1-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="658c1-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="658c1-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="658c1-166">'Blazor'</span></span>
- <span data-ttu-id="658c1-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="658c1-167">'Identity'</span></span>
- <span data-ttu-id="658c1-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="658c1-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="658c1-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="658c1-169">'Razor'</span></span>
- <span data-ttu-id="658c1-170">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="658c1-170">'SignalR' uid:</span></span> 

<span data-ttu-id="658c1-171">------ | | `None` | Сообщения не регистрируются.</span><span class="sxs-lookup"><span data-stu-id="658c1-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="658c1-172">| | `Critical` | Сообщения, указывающие на сбой во всем приложении.</span><span class="sxs-lookup"><span data-stu-id="658c1-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="658c1-173">| | `Error` | Сообщения, указывающие на сбой в текущей операции.</span><span class="sxs-lookup"><span data-stu-id="658c1-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="658c1-174">| | `Warning` | Сообщения, указывающие на некритическую проблему.</span><span class="sxs-lookup"><span data-stu-id="658c1-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="658c1-175">| | `Information` | Информационные сообщения.</span><span class="sxs-lookup"><span data-stu-id="658c1-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="658c1-176">| | `Debug` | Диагностические сообщения, полезные для отладки.</span><span class="sxs-lookup"><span data-stu-id="658c1-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="658c1-177">| | `Trace` | Очень подробные диагностические сообщения, предназначенные для диагностики конкретных проблем.</span><span class="sxs-lookup"><span data-stu-id="658c1-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="658c1-178">После настройки уровня детализации журналы записываются в консоль браузера (или стандартные выходные данные в приложении NodeJS).</span><span class="sxs-lookup"><span data-stu-id="658c1-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="658c1-179">Если вы хотите отправить журналы в пользовательскую систему ведения журнала, можно предоставить объект JavaScript, реализующий `ILogger` интерфейс.</span><span class="sxs-lookup"><span data-stu-id="658c1-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="658c1-180">Единственным методом, который необходимо реализовать `log` , является, который принимает уровень события и сообщение, связанное с событием.</span><span class="sxs-lookup"><span data-stu-id="658c1-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="658c1-181">Пример:</span><span class="sxs-lookup"><span data-stu-id="658c1-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="658c1-182">Ведение журнала клиента .NET</span><span class="sxs-lookup"><span data-stu-id="658c1-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="658c1-183">Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения.</span><span class="sxs-lookup"><span data-stu-id="658c1-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="658c1-184">**Никогда не** размещайте необработанные журналы из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="658c1-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="658c1-185">Чтобы получить журналы из клиента .NET, можно использовать `ConfigureLogging` метод для `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="658c1-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="658c1-186">Это работает так же, как `ConfigureLogging` и метод в `WebHostBuilder` и `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="658c1-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="658c1-187">Вы можете настроить те же поставщики ведения журналов, которые используются в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658c1-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="658c1-188">Однако необходимо вручную установить и включить пакеты NuGet для отдельных поставщиков ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="658c1-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="658c1-189">Журнал консоли</span><span class="sxs-lookup"><span data-stu-id="658c1-189">Console logging</span></span>

<span data-ttu-id="658c1-190">Чтобы включить ведение журнала консоли, добавьте пакет [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="658c1-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="658c1-191">Затем используйте `AddConsole` метод для настройки средства ведения журнала консоли:</span><span class="sxs-lookup"><span data-stu-id="658c1-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="658c1-192">Ведение журнала окна вывода отладки</span><span class="sxs-lookup"><span data-stu-id="658c1-192">Debug output window logging</span></span>

<span data-ttu-id="658c1-193">Кроме того, можно настроить журналы для перехода в окно **вывода** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="658c1-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="658c1-194">Установите пакет [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) и используйте `AddDebug` метод:</span><span class="sxs-lookup"><span data-stu-id="658c1-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="658c1-195">Другие поставщики ведения журнала</span><span class="sxs-lookup"><span data-stu-id="658c1-195">Other logging providers</span></span>

SignalR<span data-ttu-id="658c1-196">поддерживает другие поставщики ведения журналов, такие как Serilog, SEQ, NLog или любая другая система ведения журналов, которая интегрируется с `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="658c1-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="658c1-197">Если ваша система ведения журналов предоставляет `ILoggerProvider` , вы можете зарегистрировать ее с помощью `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="658c1-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="658c1-198">Уровень детализации элемента управления</span><span class="sxs-lookup"><span data-stu-id="658c1-198">Control verbosity</span></span>

<span data-ttu-id="658c1-199">При ведении журнала из других мест в приложении изменение уровня по умолчанию на `Debug` может оказаться слишком подробным.</span><span class="sxs-lookup"><span data-stu-id="658c1-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="658c1-200">С помощью фильтра можно настроить уровень ведения журнала для SignalR журналов.</span><span class="sxs-lookup"><span data-stu-id="658c1-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="658c1-201">Это можно сделать в коде во многом так же, как на сервере:</span><span class="sxs-lookup"><span data-stu-id="658c1-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="658c1-202">Данные трассировки сети</span><span class="sxs-lookup"><span data-stu-id="658c1-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="658c1-203">Трассировка сети содержит все содержимое каждого сообщения, отправленного приложением.</span><span class="sxs-lookup"><span data-stu-id="658c1-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="658c1-204">**Никогда не** размещайте необработанные сетевые трассировки из рабочих приложений на общедоступные форумы, такие как GitHub.</span><span class="sxs-lookup"><span data-stu-id="658c1-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="658c1-205">При возникновении проблемы трассировка сети иногда может предоставить множество полезных сведений.</span><span class="sxs-lookup"><span data-stu-id="658c1-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="658c1-206">Это особенно полезно, если вы собираетесь зафиксировать вопрос в средстве записи проблем.</span><span class="sxs-lookup"><span data-stu-id="658c1-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="658c1-207">Получение трассировки сети с помощью Fiddler (предпочтительный вариант)</span><span class="sxs-lookup"><span data-stu-id="658c1-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="658c1-208">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="658c1-208">This method works for all apps.</span></span>

<span data-ttu-id="658c1-209">Fiddler — это очень мощный инструмент для сбора трассировок HTTP.</span><span class="sxs-lookup"><span data-stu-id="658c1-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="658c1-210">Установите его из [Telerik.com/Fiddler](https://www.telerik.com/fiddler), запустите его, а затем запустите приложение и воспроизведите ошибку.</span><span class="sxs-lookup"><span data-stu-id="658c1-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="658c1-211">Fiddler доступен для Windows, и существуют бета-версии для macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="658c1-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="658c1-212">При подключении по протоколу HTTPS необходимо выполнить некоторые дополнительные действия, чтобы гарантировать, что Fiddler может расшифровать HTTPS трафик.</span><span class="sxs-lookup"><span data-stu-id="658c1-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="658c1-213">Дополнительные сведения см. в [документации по Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="658c1-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="658c1-214">После сбора трассировки можно экспортировать трассировку, выбрав **файл**  >  **сохранить**  >  **все сеансы** в строке меню.</span><span class="sxs-lookup"><span data-stu-id="658c1-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Экспорт всех сеансов из Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="658c1-216">Получение трассировки сети с помощью tcpdump (только для macOS и Linux)</span><span class="sxs-lookup"><span data-stu-id="658c1-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="658c1-217">Этот метод работает для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="658c1-217">This method works for all apps.</span></span>

<span data-ttu-id="658c1-218">Вы можете получить необработанные трассировки TCP с помощью tcpdump, выполнив следующую команду в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="658c1-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="658c1-219">Если возникает ошибка разрешений, может потребоваться добавить или добавить к `root` команде префикс `sudo` .</span><span class="sxs-lookup"><span data-stu-id="658c1-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="658c1-220">Замените на `[interface]` сетевой интерфейс, на котором вы хотите захватить.</span><span class="sxs-lookup"><span data-stu-id="658c1-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="658c1-221">Обычно это нечто вроде `/dev/eth0` (для стандартного интерфейса Ethernet) или `/dev/lo0` (для трафика localhost).</span><span class="sxs-lookup"><span data-stu-id="658c1-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="658c1-222">Дополнительные сведения см `tcpdump` . на справочной странице в главной системе.</span><span class="sxs-lookup"><span data-stu-id="658c1-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="658c1-223">Получение трассировки сети в браузере</span><span class="sxs-lookup"><span data-stu-id="658c1-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="658c1-224">Этот метод работает только для приложений на основе браузера.</span><span class="sxs-lookup"><span data-stu-id="658c1-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="658c1-225">Большинство Средства для разработчиков браузера имеют вкладку "сеть", позволяющую записывать сетевые активности между браузером и сервером.</span><span class="sxs-lookup"><span data-stu-id="658c1-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="658c1-226">Однако эти трассировки не включают WebSocket и сообщения, отправляемые сервером.</span><span class="sxs-lookup"><span data-stu-id="658c1-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="658c1-227">Если вы используете эти транспорты, лучше подходить с помощью такого средства, как Fiddler или TcpDump (описанное ниже).</span><span class="sxs-lookup"><span data-stu-id="658c1-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="658c1-228">Microsoft Edge и Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="658c1-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="658c1-229">(Инструкции одинаковы для обеих и Internet Explorer).</span><span class="sxs-lookup"><span data-stu-id="658c1-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="658c1-230">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="658c1-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="658c1-231">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="658c1-231">Click the Network Tab</span></span>
3. <span data-ttu-id="658c1-232">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="658c1-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="658c1-233">Щелкните значок "Сохранить" на панели инструментов, чтобы экспортировать трассировку в виде файла "HAR":</span><span class="sxs-lookup"><span data-stu-id="658c1-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Значок "Сохранить" на вкладке "сеть" средств разработки Microsoft ребра](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="658c1-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="658c1-235">Google Chrome</span></span>

1. <span data-ttu-id="658c1-236">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="658c1-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="658c1-237">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="658c1-237">Click the Network Tab</span></span>
3. <span data-ttu-id="658c1-238">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="658c1-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="658c1-239">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите команду "Сохранить как HAR с содержимым":</span><span class="sxs-lookup"><span data-stu-id="658c1-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Параметр "Сохранить как HAR с содержимым" на вкладке "сеть" средств разработки Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="658c1-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="658c1-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="658c1-242">Нажмите клавишу F12, чтобы открыть средства разработки</span><span class="sxs-lookup"><span data-stu-id="658c1-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="658c1-243">Перейдите на вкладку Сеть.</span><span class="sxs-lookup"><span data-stu-id="658c1-243">Click the Network Tab</span></span>
3. <span data-ttu-id="658c1-244">Обновите страницу (при необходимости) и воспроизведите проблему.</span><span class="sxs-lookup"><span data-stu-id="658c1-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="658c1-245">Щелкните правой кнопкой мыши в любом месте списка запросов и выберите "сохранить все как HAR".</span><span class="sxs-lookup"><span data-stu-id="658c1-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Параметр "сохранить все как HAR" на вкладке "сеть" средств разработки Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="658c1-247">Присоединение файлов диагностики к проблемам GitHub</span><span class="sxs-lookup"><span data-stu-id="658c1-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="658c1-248">Вы можете присоединить диагностические файлы к проблемам GitHub, переименовав их, чтобы они имели `.txt` расширение, а затем перетащив их на ошибку.</span><span class="sxs-lookup"><span data-stu-id="658c1-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="658c1-249">Не вставляйте содержимое файлов журнала или трассировки сети в проблемы GitHub.</span><span class="sxs-lookup"><span data-stu-id="658c1-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="658c1-250">Эти журналы и трассировки могут быть довольно большими, и GitHub обычно усекает их.</span><span class="sxs-lookup"><span data-stu-id="658c1-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Перетаскивание файлов журнала на вопрос GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="658c1-252">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="658c1-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
