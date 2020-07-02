---
title: Обнаружение изменений с помощью токенов изменений в ASP.NET Core
author: rick-anderson
description: Сведения об использовании токенов изменений для отслеживания изменений.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: dd271f9db016c35eb072749258a499168b39010f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408439"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="7c6a8-103">Обнаружение изменений с помощью токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c6a8-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c6a8-104">*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="7c6a8-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c6a8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="7c6a8-106">Интерфейс IChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-106">IChangeToken interface</span></span>

<span data-ttu-id="7c6a8-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="7c6a8-108">`IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7c6a8-109">Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="7c6a8-110">`IChangeToken` имеет два свойства:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="7c6a8-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="7c6a8-112">Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="7c6a8-113">Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="7c6a8-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="7c6a8-115">Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="7c6a8-116">Перед выполнением обратного вызова нужно задать `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="7c6a8-117">Класс ChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-117">ChangeToken class</span></span>

<span data-ttu-id="7c6a8-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="7c6a8-119">`ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7c6a8-120">Пакет [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet явно предоставляется приложениям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="7c6a8-121">Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="7c6a8-122">`Func<IChangeToken>` создает токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="7c6a8-123">`Action` вызывается при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="7c6a8-124">Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="7c6a8-125">`OnChange` возвращает <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="7c6a8-126">При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="7c6a8-127">Примеры использования токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c6a8-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="7c6a8-128">Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="7c6a8-129">Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="7c6a8-130">Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="7c6a8-131">Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="7c6a8-132">Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="7c6a8-133">Отслеживание изменений конфигурации</span><span class="sxs-lookup"><span data-stu-id="7c6a8-133">Monitor for configuration changes</span></span>

<span data-ttu-id="7c6a8-134">По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="7c6a8-135">Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="7c6a8-136">`reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="7c6a8-137">Этот параметр отображается в удобном методе <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> класса <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="7c6a8-138">Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="7c6a8-139">`FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="7c6a8-140">По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="7c6a8-141">Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="7c6a8-142">Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="7c6a8-143">`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="7c6a8-144">Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="7c6a8-145">В примере используется хэширование файлов SHA1.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="7c6a8-146">Повторная попытка реализуется посредством экспоненциальной задержки.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="7c6a8-147">Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="7c6a8-148">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="7c6a8-149">Токен изменений для простого запуска</span><span class="sxs-lookup"><span data-stu-id="7c6a8-149">Simple startup change token</span></span>

<span data-ttu-id="7c6a8-150">Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="7c6a8-151">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="7c6a8-152">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="7c6a8-153">Обратный вызов является методом `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="7c6a8-154">`state` обратного вызова используется для передачи `IWebHostEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="7c6a8-155">Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="7c6a8-156">Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="7c6a8-157">Отслеживание изменений конфигурации как служба</span><span class="sxs-lookup"><span data-stu-id="7c6a8-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="7c6a8-158">Этот пример реализует следующее:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-158">The sample implements:</span></span>

* <span data-ttu-id="7c6a8-159">Базовое отслеживание токена запуска.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="7c6a8-160">Отслеживание как служба.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-160">Monitoring as a service.</span></span>
* <span data-ttu-id="7c6a8-161">Механизм для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="7c6a8-162">Этот пример задает интерфейс `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="7c6a8-163">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="7c6a8-164">Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="7c6a8-165">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="7c6a8-166">`InvokeChanged` является методом обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="7c6a8-167">`state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="7c6a8-168">Используются два свойства:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-168">Two properties are used:</span></span>

* <span data-ttu-id="7c6a8-169">`MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="7c6a8-170">`CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="7c6a8-171">Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="7c6a8-172">не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;</span><span class="sxs-lookup"><span data-stu-id="7c6a8-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="7c6a8-173">выводит текущее значение `state` в своих выходных данных `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="7c6a8-174">Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="7c6a8-175">Страница индекса позволяет пользователю управлять отслеживанием конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="7c6a8-176">Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="7c6a8-177">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7c6a8-178">Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="7c6a8-179">При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="7c6a8-180">При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="7c6a8-181">Кнопки пользовательского интерфейса для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="7c6a8-182">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="7c6a8-183">Отслеживание изменений кэшированных файлов</span><span class="sxs-lookup"><span data-stu-id="7c6a8-183">Monitor cached file changes</span></span>

<span data-ttu-id="7c6a8-184">Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="7c6a8-185">Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="7c6a8-186">*Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="7c6a8-187">Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="7c6a8-188">Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="7c6a8-189">Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="7c6a8-190">Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="7c6a8-191">Этот пример демонстрирует реализацию данного подхода.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="7c6a8-192">`GetFileContent` в примере используется для:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="7c6a8-193">возврата содержимого файла;</span><span class="sxs-lookup"><span data-stu-id="7c6a8-193">Return file content.</span></span>
* <span data-ttu-id="7c6a8-194">реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="7c6a8-195">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="7c6a8-196">Для обработки поиска кэшированных файлов создается `FileService`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="7c6a8-197">Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="7c6a8-198">Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="7c6a8-199">Содержимое файла получается с помощью `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="7c6a8-200">Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="7c6a8-201">При изменении файла активируется обратный вызов токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="7c6a8-202">Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="7c6a8-203">Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="7c6a8-204">В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="7c6a8-205">`IWebHostEnvironment.ContentRootFileProvider` используется для получения <xref:Microsoft.Extensions.FileProviders.IFileProvider> с указанием на приложение `IWebHostEnvironment.ContentRootPath`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="7c6a8-206">Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="7c6a8-207">`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="7c6a8-208">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="7c6a8-209">Страничная модель загружает содержимое файла с помощью службы.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="7c6a8-210">На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="7c6a8-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="7c6a8-211">Класс CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-211">CompositeChangeToken class</span></span>

<span data-ttu-id="7c6a8-212">Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="7c6a8-213">`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="7c6a8-214">`ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="7c6a8-215">Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c6a8-216">*Токен изменений* — это низкоуровневый стандартный блок общего назначения, используемый для отслеживания изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="7c6a8-217">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c6a8-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="7c6a8-218">Интерфейс IChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-218">IChangeToken interface</span></span>

<span data-ttu-id="7c6a8-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="7c6a8-220">`IChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7c6a8-221">Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="7c6a8-222">`IChangeToken` имеет два свойства:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="7c6a8-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> указывает, выполняет ли токен обратные вызовы с упреждением.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="7c6a8-224">Если для `ActiveChangedCallbacks` задано значение `false`, обратный вызов не выполняется, а приложению нужно опросить `HasChanged` на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="7c6a8-225">Кроме того, отмена токена может никогда не произойти в случае отсутствия изменений или отключения либо удаления базового прослушивателя изменений.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="7c6a8-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> получает значение, указывающее, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="7c6a8-227">Интерфейс `IChangeToken` включает метод [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), который регистрирует обратный вызов, выполняемый при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="7c6a8-228">Перед выполнением обратного вызова нужно задать `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="7c6a8-229">Класс ChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-229">ChangeToken class</span></span>

<span data-ttu-id="7c6a8-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> — это статический класс, который распространяет уведомления о том, что произошло изменение.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="7c6a8-231">`ChangeToken` находится в пространстве имен <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="7c6a8-232">Для приложений, которые не используют метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), создайте ссылку на пакет NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="7c6a8-233">Метод [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) регистрирует `Action` для вызова при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="7c6a8-234">`Func<IChangeToken>` создает токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="7c6a8-235">`Action` вызывается при изменении токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="7c6a8-236">Перегрузка [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) принимает дополнительный параметр `TState`, передаваемый в `Action` объекта-получатель токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="7c6a8-237">`OnChange` возвращает <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="7c6a8-238">При вызове <xref:System.IDisposable.Dispose*> токен прекращает прослушивать дальнейшие изменения и освобождает свои ресурсы.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="7c6a8-239">Примеры использования токенов изменений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c6a8-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="7c6a8-240">Токены изменений используются в ключевых областях ASP.NET Core для отслеживания изменений в объектах:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="7c6a8-241">Чтобы отслеживать изменения в файлах, метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> интерфейса <xref:Microsoft.Extensions.FileProviders.IFileProvider> создает `IChangeToken` для указанных файлов или папки.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="7c6a8-242">Токены `IChangeToken` можно добавлять в записи кэша, чтобы активировать вытеснения кэша при изменении.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="7c6a8-243">Для изменений `TOptions` используемая по умолчанию реализация <xref:Microsoft.Extensions.Options.OptionsMonitor`1> интерфейса <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> имеет перегрузку, которая принимает один или несколько экземпляров <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="7c6a8-244">Каждый экземпляр возвращает `IChangeToken`, чтобы зарегистрировать обратный вызов уведомления об изменении для отслеживания изменений параметров.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="7c6a8-245">Отслеживание изменений конфигурации</span><span class="sxs-lookup"><span data-stu-id="7c6a8-245">Monitor for configuration changes</span></span>

<span data-ttu-id="7c6a8-246">По умолчанию шаблоны ASP.NET Core используют [файлы конфигурации JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* и *appsettings.Production.json*) для загрузки параметров конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="7c6a8-247">Эти файлы настраиваются с помощью метода расширения [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) в <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, который принимает параметр `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="7c6a8-248">`reloadOnChange` указывает, нужно ли перезагружать конфигурацию при изменении файла.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="7c6a8-249">Этот параметр отображается в удобном методе <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> класса <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="7c6a8-250">Конфигурация на основе файла представлена <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="7c6a8-251">`FileConfigurationSource` использует <xref:Microsoft.Extensions.FileProviders.IFileProvider> для отслеживания файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="7c6a8-252">По умолчанию `IFileMonitor` предоставляется объектом <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, который использует <xref:System.IO.FileSystemWatcher> для отслеживания изменений в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="7c6a8-253">Этот пример приложения демонстрирует две реализации для отслеживания изменений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="7c6a8-254">Если какой-либо из файлов *appsettings* был изменен, каждая реализация отслеживания файлов выполняет пользовательский код &mdash; пример приложения записывает сообщение на консоль.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="7c6a8-255">`FileSystemWatcher` файла конфигурации может активировать несколько обратных вызовов токена для одного изменения файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="7c6a8-256">Чтобы пользовательский код выполнялся один раз при активации нескольких обратных вызовов токена, реализация в примере проверяет хэши файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="7c6a8-257">В примере используется хэширование файлов SHA1.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="7c6a8-258">Повторная попытка реализуется посредством экспоненциальной задержки.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="7c6a8-259">Повторная попытка нужна, так как может возникать блокировка файлов, препятствующая вычислению нового хэша в файле.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="7c6a8-260">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="7c6a8-261">Токен изменений для простого запуска</span><span class="sxs-lookup"><span data-stu-id="7c6a8-261">Simple startup change token</span></span>

<span data-ttu-id="7c6a8-262">Зарегистрируйте обратный вызов `Action` объекта-получателя токена для уведомлений об изменениях в токене перезагрузки конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="7c6a8-263">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="7c6a8-264">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="7c6a8-265">Обратный вызов является методом `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="7c6a8-266">`state` обратного вызова используется для передачи `IHostingEnvironment`. Это удобно для определения правильного JSON-файла конфигурации *appsettings*, который требуется отслеживать (например, *appsettings.Development.json* при работе в среде разработки).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="7c6a8-267">Хэши файлов используются для предотвращения многократного выполнения оператора `WriteConsole` из-за нескольких обратных вызовов токена при всего одном изменении файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="7c6a8-268">Эта система выполняется, пока запущено приложение, и не может быть отключена пользователем.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="7c6a8-269">Отслеживание изменений конфигурации как служба</span><span class="sxs-lookup"><span data-stu-id="7c6a8-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="7c6a8-270">Этот пример реализует следующее:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-270">The sample implements:</span></span>

* <span data-ttu-id="7c6a8-271">Базовое отслеживание токена запуска.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="7c6a8-272">Отслеживание как служба.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-272">Monitoring as a service.</span></span>
* <span data-ttu-id="7c6a8-273">Механизм для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="7c6a8-274">Этот пример задает интерфейс `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="7c6a8-275">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="7c6a8-276">Конструктор реализованного класса `ConfigurationMonitor` регистрирует обратный вызов для уведомлений об изменениях:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="7c6a8-277">`config.GetReloadToken()` предоставляет токен.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="7c6a8-278">`InvokeChanged` является методом обратного вызова.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="7c6a8-279">`state` в этом экземпляре является ссылкой на экземпляр `IConfigurationMonitor`, используемый для доступа к состоянию мониторинга.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="7c6a8-280">Используются два свойства:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-280">Two properties are used:</span></span>

* <span data-ttu-id="7c6a8-281">`MonitoringEnabled`. Указывает, нужно ли обратному вызову выполнять свой настраиваемый код.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="7c6a8-282">`CurrentState`. Описывает текущее состояние отслеживания для использования в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="7c6a8-283">Метод `InvokeChanged` похож на описанный ранее подход, за исключением того, что он:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="7c6a8-284">не выполняет свой код, если только `MonitoringEnabled` не имеет значение `true`;</span><span class="sxs-lookup"><span data-stu-id="7c6a8-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="7c6a8-285">выводит текущее значение `state` в своих выходных данных `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="7c6a8-286">Экземпляр `ConfigurationMonitor` регистрируется как служба в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="7c6a8-287">Страница индекса позволяет пользователю управлять отслеживанием конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="7c6a8-288">Экземпляр `IConfigurationMonitor` внедряется в `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="7c6a8-289">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7c6a8-290">Монитор конфигурации (`_monitor`) позволяет включить или отключить мониторинг и задать текущее состояние для обратной связи пользовательского интерфейса:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="7c6a8-291">При активации `OnPostStartMonitoring` отслеживание включается, а текущее состояние сбрасывается.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="7c6a8-292">При активации `OnPostStopMonitoring` отслеживание отключается, а состояние указывает на отсутствие отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="7c6a8-293">Кнопки пользовательского интерфейса для включения и отключения отслеживания.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="7c6a8-294">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="7c6a8-295">Отслеживание изменений кэшированных файлов</span><span class="sxs-lookup"><span data-stu-id="7c6a8-295">Monitor cached file changes</span></span>

<span data-ttu-id="7c6a8-296">Содержимое файла можно кэшировать в памяти с помощью <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="7c6a8-297">Кэширование в памяти описано в разделе [Кэш в памяти](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="7c6a8-298">*Устаревшие* (просроченные) данные возвращаются из кэша при изменении источника исходных данных без каких-либо дополнительных действий, таких как описанная ниже реализация.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="7c6a8-299">Например, если не учитывать состояние кэшированного исходного файла при продлении [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), это приведет к появлению в кэше устаревших данных файлов.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="7c6a8-300">Каждый запрос к данным продляет скользящий срок действия, но этот файл никогда не загружается в кэш.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="7c6a8-301">Любые функции приложения, использующие кэшированное содержимое, могут получить устаревшее содержимое.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="7c6a8-302">Использование токенов изменений в сценарии кэширования файлов предотвращает появление устаревшего содержимого файлов в кэше.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="7c6a8-303">Этот пример демонстрирует реализацию данного подхода.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="7c6a8-304">`GetFileContent` в примере используется для:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="7c6a8-305">возврата содержимого файла;</span><span class="sxs-lookup"><span data-stu-id="7c6a8-305">Return file content.</span></span>
* <span data-ttu-id="7c6a8-306">реализации алгоритма повторных попыток с экспоненциальной задержкой, чтобы охватить ситуации, когда блокировка файла временно препятствует его считыванию.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="7c6a8-307">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="7c6a8-308">Для обработки поиска кэшированных файлов создается `FileService`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="7c6a8-309">Направленный в службу вызов метода `GetFileContent` пытается получить содержимое файла из кэша в памяти и вернуть его вызывающему объекту (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="7c6a8-310">Если кэшированное содержимое не удается найти по ключу кэша, выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="7c6a8-311">Содержимое файла получается с помощью `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="7c6a8-312">Токен изменений извлекается из файла поставщика с помощью [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="7c6a8-313">При изменении файла активируется обратный вызов токена.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="7c6a8-314">Содержимое файла кэшируется с использованием [скользящего срока действия](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="7c6a8-315">Токен изменений подключается к [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) для исключения записи кэша, если файл изменяется во время его кэширования.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="7c6a8-316">В следующем примере файлы хранятся в [корневом каталоге содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="7c6a8-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) используется для получения объекта <xref:Microsoft.Extensions.FileProviders.IFileProvider>, который указывает на <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> приложения.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="7c6a8-318">Для получения `filePath` используется [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="7c6a8-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="7c6a8-319">`FileService` регистрируется в контейнере службы вместе со службой кэширования памяти.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="7c6a8-320">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c6a8-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="7c6a8-321">Страничная модель загружает содержимое файла с помощью службы.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="7c6a8-322">На странице индексов используется метод `OnGet` (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="7c6a8-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="7c6a8-323">Класс CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="7c6a8-323">CompositeChangeToken class</span></span>

<span data-ttu-id="7c6a8-324">Чтобы представить один или несколько экземпляров `IChangeToken` в одном объекте, используйте класс <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="7c6a8-325">`HasChanged` для составного токена указывает `true`, если `HasChanged` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="7c6a8-326">`ActiveChangeCallbacks` для составного токена указывает `true`, если `ActiveChangeCallbacks` любой из представленных токенов имеет значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="7c6a8-327">Если возникает несколько одновременных событий изменения, обратный вызов составного изменения выполняется один раз.</span><span class="sxs-lookup"><span data-stu-id="7c6a8-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7c6a8-328">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7c6a8-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
