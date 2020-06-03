---
<span data-ttu-id="70a86-101">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="70a86-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="70a86-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="70a86-102">'Blazor'</span></span>
- <span data-ttu-id="70a86-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="70a86-103">'Identity'</span></span>
- <span data-ttu-id="70a86-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="70a86-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="70a86-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="70a86-105">'Razor'</span></span>
- <span data-ttu-id="70a86-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="70a86-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="70a86-107">Обнаружение изменений с помощью токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70a86-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70a86-108">*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="70a86-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70a86-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="70a86-110">Интерфейс IChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-110">IChangeToken interface</span></span>

<span data-ttu-id="70a86-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="70a86-112">`IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="70a86-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="70a86-113">Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70a86-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="70a86-114">`IChangeToken` имеет два свойства:</span><span class="sxs-lookup"><span data-stu-id="70a86-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="70a86-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением.</span><span class="sxs-lookup"><span data-stu-id="70a86-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="70a86-116">Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="70a86-117">Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="70a86-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="70a86-119">Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="70a86-120">Перед выполнением обратного вызова нужно задать `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="70a86-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="70a86-121">Класс ChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-121">ChangeToken class</span></span>

<span data-ttu-id="70a86-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="70a86-123">`ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="70a86-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="70a86-124">Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70a86-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="70a86-125">Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="70a86-126">`Func<IChangeToken>` создает токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="70a86-127">`Action` вызывается при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="70a86-128">Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="70a86-129">`OnChange` возвращает <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="70a86-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="70a86-130">При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.</span><span class="sxs-lookup"><span data-stu-id="70a86-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="70a86-131">Примеры использования токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70a86-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="70a86-132">Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:</span><span class="sxs-lookup"><span data-stu-id="70a86-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="70a86-133">Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.</span><span class="sxs-lookup"><span data-stu-id="70a86-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="70a86-134">Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.</span><span class="sxs-lookup"><span data-stu-id="70a86-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="70a86-135">Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="70a86-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="70a86-136">Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.</span><span class="sxs-lookup"><span data-stu-id="70a86-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="70a86-137">Отслеживание изменений конфигурации</span><span class="sxs-lookup"><span data-stu-id="70a86-137">Monitor for configuration changes</span></span>

<span data-ttu-id="70a86-138">По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="70a86-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="70a86-139">Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="70a86-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="70a86-140">`reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла.</span><span class="sxs-lookup"><span data-stu-id="70a86-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="70a86-141">Этот параметр отображается в удобном методе <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> класса <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="70a86-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="70a86-142">Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="70a86-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="70a86-143">`FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="70a86-144">По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="70a86-145">Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="70a86-146">Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.</span><span class="sxs-lookup"><span data-stu-id="70a86-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="70a86-147">`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="70a86-148">Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="70a86-149">В примере используется хэширование файлов SHA1.</span><span class="sxs-lookup"><span data-stu-id="70a86-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="70a86-150">Повторная попытка реализуется посредством экспоненциальной задержки.</span><span class="sxs-lookup"><span data-stu-id="70a86-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="70a86-151">Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.</span><span class="sxs-lookup"><span data-stu-id="70a86-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="70a86-152">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="70a86-153">Токен изменений для простого запуска</span><span class="sxs-lookup"><span data-stu-id="70a86-153">Simple startup change token</span></span>

<span data-ttu-id="70a86-154">Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="70a86-155">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="70a86-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="70a86-156">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="70a86-157">Обратный вызов является методом `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="70a86-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="70a86-158">`state` обратного вызова используется для передачи `IWebHostEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки).</span><span class="sxs-lookup"><span data-stu-id="70a86-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="70a86-159">Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="70a86-160">Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.</span><span class="sxs-lookup"><span data-stu-id="70a86-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="70a86-161">Отслеживание изменений конфигурации как служба</span><span class="sxs-lookup"><span data-stu-id="70a86-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="70a86-162">Этот пример реализует следующее:</span><span class="sxs-lookup"><span data-stu-id="70a86-162">The sample implements:</span></span>

* <span data-ttu-id="70a86-163">Базовое отслеживание токена запуска.</span><span class="sxs-lookup"><span data-stu-id="70a86-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="70a86-164">Отслеживание как служба.</span><span class="sxs-lookup"><span data-stu-id="70a86-164">Monitoring as a service.</span></span>
* <span data-ttu-id="70a86-165">Механизм для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="70a86-166">Этот пример задает интерфейс `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="70a86-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="70a86-167">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="70a86-168">Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:</span><span class="sxs-lookup"><span data-stu-id="70a86-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="70a86-169">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="70a86-170">`InvokeChanged` является методом обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="70a86-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="70a86-171">`state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга.</span><span class="sxs-lookup"><span data-stu-id="70a86-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="70a86-172">Используются два свойства:</span><span class="sxs-lookup"><span data-stu-id="70a86-172">Two properties are used:</span></span>

* <span data-ttu-id="70a86-173">`MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.</span><span class="sxs-lookup"><span data-stu-id="70a86-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="70a86-174">`CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="70a86-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="70a86-175">Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:</span><span class="sxs-lookup"><span data-stu-id="70a86-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="70a86-176">не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;</span><span class="sxs-lookup"><span data-stu-id="70a86-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="70a86-177">выводит текущее значение `state` в своих выходных данных `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="70a86-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="70a86-178">Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70a86-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="70a86-179">Страница индекса позволяет пользователю управлять отслеживанием конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="70a86-180">Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="70a86-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="70a86-181">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="70a86-182">Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:</span><span class="sxs-lookup"><span data-stu-id="70a86-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="70a86-183">При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается.</span><span class="sxs-lookup"><span data-stu-id="70a86-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="70a86-184">При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="70a86-185">Кнопки пользовательского интерфейса для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="70a86-186">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70a86-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="70a86-187">Отслеживание изменений кэшированных файлов</span><span class="sxs-lookup"><span data-stu-id="70a86-187">Monitor cached file changes</span></span>

<span data-ttu-id="70a86-188">Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="70a86-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="70a86-189">Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="70a86-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="70a86-190">*Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.</span><span class="sxs-lookup"><span data-stu-id="70a86-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="70a86-191">Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="70a86-192">Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш.</span><span class="sxs-lookup"><span data-stu-id="70a86-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="70a86-193">Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.</span><span class="sxs-lookup"><span data-stu-id="70a86-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="70a86-194">Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше.</span><span class="sxs-lookup"><span data-stu-id="70a86-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="70a86-195">Этот пример демонстрирует реализацию данного подхода.</span><span class="sxs-lookup"><span data-stu-id="70a86-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="70a86-196">`GetFileContent` в примере используется для:</span><span class="sxs-lookup"><span data-stu-id="70a86-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="70a86-197">возврата содержимого файла;</span><span class="sxs-lookup"><span data-stu-id="70a86-197">Return file content.</span></span>
* <span data-ttu-id="70a86-198">реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.</span><span class="sxs-lookup"><span data-stu-id="70a86-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="70a86-199">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="70a86-200">Для обработки поиска кэшированных файлов создается `FileService`.</span><span class="sxs-lookup"><span data-stu-id="70a86-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="70a86-201">Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="70a86-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="70a86-202">Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="70a86-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="70a86-203">Содержимое файла получается с помощью `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="70a86-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="70a86-204">Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="70a86-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="70a86-205">При изменении файла активируется обратный вызов токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="70a86-206">Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="70a86-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="70a86-207">Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.</span><span class="sxs-lookup"><span data-stu-id="70a86-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="70a86-208">В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="70a86-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="70a86-209">`IWebHostEnvironment.ContentRootFileProvider` используется для получения <xref:Microsoft.Extensions.FileProviders.IFileProvider> с указанием на приложение `IWebHostEnvironment.ContentRootPath`.</span><span class="sxs-lookup"><span data-stu-id="70a86-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="70a86-210">Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="70a86-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="70a86-211">`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.</span><span class="sxs-lookup"><span data-stu-id="70a86-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="70a86-212">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70a86-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="70a86-213">Страничная модель загружает содержимое файла с помощью службы.</span><span class="sxs-lookup"><span data-stu-id="70a86-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="70a86-214">На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="70a86-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="70a86-215">Класс CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-215">CompositeChangeToken class</span></span>

<span data-ttu-id="70a86-216">Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="70a86-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="70a86-217">`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="70a86-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="70a86-218">`ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="70a86-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="70a86-219">Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.</span><span class="sxs-lookup"><span data-stu-id="70a86-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="70a86-220">*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="70a86-221">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70a86-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="70a86-222">Интерфейс IChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-222">IChangeToken interface</span></span>

<span data-ttu-id="70a86-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="70a86-224">`IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="70a86-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="70a86-225">Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="70a86-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="70a86-226">`IChangeToken` имеет два свойства:</span><span class="sxs-lookup"><span data-stu-id="70a86-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="70a86-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением.</span><span class="sxs-lookup"><span data-stu-id="70a86-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="70a86-228">Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="70a86-229">Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.</span><span class="sxs-lookup"><span data-stu-id="70a86-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="70a86-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="70a86-231">Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="70a86-232">Перед выполнением обратного вызова нужно задать `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="70a86-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="70a86-233">Класс ChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-233">ChangeToken class</span></span>

<span data-ttu-id="70a86-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="70a86-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="70a86-235">`ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="70a86-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="70a86-236">Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="70a86-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="70a86-237">Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="70a86-238">`Func<IChangeToken>` создает токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="70a86-239">`Action` вызывается при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="70a86-240">Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="70a86-241">`OnChange` возвращает <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="70a86-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="70a86-242">При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.</span><span class="sxs-lookup"><span data-stu-id="70a86-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="70a86-243">Примеры использования токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70a86-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="70a86-244">Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:</span><span class="sxs-lookup"><span data-stu-id="70a86-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="70a86-245">Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.</span><span class="sxs-lookup"><span data-stu-id="70a86-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="70a86-246">Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.</span><span class="sxs-lookup"><span data-stu-id="70a86-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="70a86-247">Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="70a86-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="70a86-248">Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.</span><span class="sxs-lookup"><span data-stu-id="70a86-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="70a86-249">Отслеживание изменений конфигурации</span><span class="sxs-lookup"><span data-stu-id="70a86-249">Monitor for configuration changes</span></span>

<span data-ttu-id="70a86-250">По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="70a86-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="70a86-251">Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="70a86-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="70a86-252">`reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла.</span><span class="sxs-lookup"><span data-stu-id="70a86-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="70a86-253">Этот параметр отображается в удобном методе <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> класса <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="70a86-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="70a86-254">Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="70a86-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="70a86-255">`FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="70a86-256">По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="70a86-257">Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="70a86-258">Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.</span><span class="sxs-lookup"><span data-stu-id="70a86-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="70a86-259">`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="70a86-260">Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="70a86-261">В примере используется хэширование файлов SHA1.</span><span class="sxs-lookup"><span data-stu-id="70a86-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="70a86-262">Повторная попытка реализуется посредством экспоненциальной задержки.</span><span class="sxs-lookup"><span data-stu-id="70a86-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="70a86-263">Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.</span><span class="sxs-lookup"><span data-stu-id="70a86-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="70a86-264">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="70a86-265">Токен изменений для простого запуска</span><span class="sxs-lookup"><span data-stu-id="70a86-265">Simple startup change token</span></span>

<span data-ttu-id="70a86-266">Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="70a86-267">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="70a86-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="70a86-268">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="70a86-269">Обратный вызов является методом `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="70a86-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="70a86-270">`state` обратного вызова используется для передачи `IHostingEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки).</span><span class="sxs-lookup"><span data-stu-id="70a86-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="70a86-271">Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="70a86-272">Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.</span><span class="sxs-lookup"><span data-stu-id="70a86-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="70a86-273">Отслеживание изменений конфигурации как служба</span><span class="sxs-lookup"><span data-stu-id="70a86-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="70a86-274">Этот пример реализует следующее:</span><span class="sxs-lookup"><span data-stu-id="70a86-274">The sample implements:</span></span>

* <span data-ttu-id="70a86-275">Базовое отслеживание токена запуска.</span><span class="sxs-lookup"><span data-stu-id="70a86-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="70a86-276">Отслеживание как служба.</span><span class="sxs-lookup"><span data-stu-id="70a86-276">Monitoring as a service.</span></span>
* <span data-ttu-id="70a86-277">Механизм для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="70a86-278">Этот пример задает интерфейс `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="70a86-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="70a86-279">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="70a86-280">Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:</span><span class="sxs-lookup"><span data-stu-id="70a86-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="70a86-281">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="70a86-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="70a86-282">`InvokeChanged` является методом обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="70a86-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="70a86-283">`state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга.</span><span class="sxs-lookup"><span data-stu-id="70a86-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="70a86-284">Используются два свойства:</span><span class="sxs-lookup"><span data-stu-id="70a86-284">Two properties are used:</span></span>

* <span data-ttu-id="70a86-285">`MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.</span><span class="sxs-lookup"><span data-stu-id="70a86-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="70a86-286">`CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="70a86-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="70a86-287">Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:</span><span class="sxs-lookup"><span data-stu-id="70a86-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="70a86-288">не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;</span><span class="sxs-lookup"><span data-stu-id="70a86-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="70a86-289">выводит текущее значение `state` в своих выходных данных `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="70a86-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="70a86-290">Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70a86-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="70a86-291">Страница индекса позволяет пользователю управлять отслеживанием конфигурации.</span><span class="sxs-lookup"><span data-stu-id="70a86-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="70a86-292">Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="70a86-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="70a86-293">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="70a86-294">Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:</span><span class="sxs-lookup"><span data-stu-id="70a86-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="70a86-295">При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается.</span><span class="sxs-lookup"><span data-stu-id="70a86-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="70a86-296">При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="70a86-297">Кнопки пользовательского интерфейса для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="70a86-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="70a86-298">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70a86-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="70a86-299">Отслеживание изменений кэшированных файлов</span><span class="sxs-lookup"><span data-stu-id="70a86-299">Monitor cached file changes</span></span>

<span data-ttu-id="70a86-300">Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="70a86-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="70a86-301">Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="70a86-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="70a86-302">*Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.</span><span class="sxs-lookup"><span data-stu-id="70a86-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="70a86-303">Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов.</span><span class="sxs-lookup"><span data-stu-id="70a86-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="70a86-304">Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш.</span><span class="sxs-lookup"><span data-stu-id="70a86-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="70a86-305">Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.</span><span class="sxs-lookup"><span data-stu-id="70a86-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="70a86-306">Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше.</span><span class="sxs-lookup"><span data-stu-id="70a86-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="70a86-307">Этот пример демонстрирует реализацию данного подхода.</span><span class="sxs-lookup"><span data-stu-id="70a86-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="70a86-308">`GetFileContent` в примере используется для:</span><span class="sxs-lookup"><span data-stu-id="70a86-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="70a86-309">возврата содержимого файла;</span><span class="sxs-lookup"><span data-stu-id="70a86-309">Return file content.</span></span>
* <span data-ttu-id="70a86-310">реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.</span><span class="sxs-lookup"><span data-stu-id="70a86-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="70a86-311">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="70a86-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="70a86-312">Для обработки поиска кэшированных файлов создается `FileService`.</span><span class="sxs-lookup"><span data-stu-id="70a86-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="70a86-313">Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="70a86-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="70a86-314">Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="70a86-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="70a86-315">Содержимое файла получается с помощью `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="70a86-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="70a86-316">Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="70a86-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="70a86-317">При изменении файла активируется обратный вызов токена.</span><span class="sxs-lookup"><span data-stu-id="70a86-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="70a86-318">Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="70a86-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="70a86-319">Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.</span><span class="sxs-lookup"><span data-stu-id="70a86-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="70a86-320">В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="70a86-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="70a86-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) используется для получения объекта <xref:Microsoft.Extensions.FileProviders.IFileProvider>, который указывает на <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> приложения.</span><span class="sxs-lookup"><span data-stu-id="70a86-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="70a86-322">Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="70a86-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="70a86-323">`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.</span><span class="sxs-lookup"><span data-stu-id="70a86-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="70a86-324">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70a86-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="70a86-325">Страничная модель загружает содержимое файла с помощью службы.</span><span class="sxs-lookup"><span data-stu-id="70a86-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="70a86-326">На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="70a86-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="70a86-327">Класс CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="70a86-327">CompositeChangeToken class</span></span>

<span data-ttu-id="70a86-328">Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="70a86-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="70a86-329">`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="70a86-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="70a86-330">`ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="70a86-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="70a86-331">Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.</span><span class="sxs-lookup"><span data-stu-id="70a86-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="70a86-332">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="70a86-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
