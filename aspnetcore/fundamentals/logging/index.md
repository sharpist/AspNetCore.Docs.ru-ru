---
title: Ведение журнала в .NET Core и ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать платформу ведения журналов, предоставляемую пакетом NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330771"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="1f24b-103">Ведение журнала в .NET Core и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f24b-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f24b-104">Авторы: [Кирк Ларкин (Kirk Larkin)](https://twitter.com/serpent5), [Юрген Гутч (Juergen Gutsch)](https://github.com/JuergenGutsch) и [Рик Андерсн (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f24b-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1f24b-105">.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="1f24b-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1f24b-106">В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="1f24b-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1f24b-107">Большинство примеров кода, приведенных в этой статье, взяты из приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="1f24b-108">Части этих фрагментов кода, относящиеся к ведению журнала, применимы ко всем приложениям .NET Core, использующим [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="1f24b-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="1f24b-109">Шаблоны веб-приложений ASP.NET Core используют универсальный узел.</span><span class="sxs-lookup"><span data-stu-id="1f24b-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="1f24b-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f24b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="1f24b-111">Поставщики ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-111">Logging providers</span></span>

<span data-ttu-id="1f24b-112">Поставщики ведения журнала обеспечивают хранение журналов, за исключением поставщика `Console`, который служит для их отображения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="1f24b-113">Например, поставщик Azure Application Insights хранит журналы в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1f24b-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="1f24b-114">Вы можете включить несколько поставщиков.</span><span class="sxs-lookup"><span data-stu-id="1f24b-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="1f24b-115">Шаблоны по умолчанию для веб-приложений ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f24b-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="1f24b-116">используют [универсальный узел](xref:fundamentals/host/generic-host);</span><span class="sxs-lookup"><span data-stu-id="1f24b-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="1f24b-117">вызывают метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="1f24b-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="1f24b-118">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-118">Console</span></span>](#console)
  * [<span data-ttu-id="1f24b-119">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="1f24b-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="1f24b-121">[EventLog](#welog): Только для Windows</span><span class="sxs-lookup"><span data-stu-id="1f24b-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="1f24b-122">В предыдущем коде показан класс `Program`, созданный с использованием шаблонов веб-приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="1f24b-123">В следующих разделах приводятся примеры, которые построены на основе шаблонов веб-приложений ASP.NET Core и используют универсальный узел.</span><span class="sxs-lookup"><span data-stu-id="1f24b-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="1f24b-124">[Консольные приложения без размещения](#nhca) рассматриваются далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="1f24b-125">Чтобы переопределить заданный по умолчанию набор поставщиков ведения журнала, добавленных с помощью `Host.CreateDefaultBuilder`, вызовите метод `ClearProviders` и добавьте необходимые поставщики.</span><span class="sxs-lookup"><span data-stu-id="1f24b-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="1f24b-126">Например, приведенный ниже код</span><span class="sxs-lookup"><span data-stu-id="1f24b-126">For example, the following code:</span></span>

* <span data-ttu-id="1f24b-127">вызывает метод <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> для удаления всех экземпляров <xref:Microsoft.Extensions.Logging.ILoggerProvider> из построителя;</span><span class="sxs-lookup"><span data-stu-id="1f24b-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="1f24b-128">добавляет поставщик ведения журнала [Console](#console).</span><span class="sxs-lookup"><span data-stu-id="1f24b-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="1f24b-129">Сведения о других поставщиках см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="1f24b-129">For additional providers, see:</span></span>

* <span data-ttu-id="1f24b-130">[Встроенные поставщики ведения журнала](#bilp).</span><span class="sxs-lookup"><span data-stu-id="1f24b-130">[Built-in logging providers](#bilp)</span></span>
* <span data-ttu-id="1f24b-131">[Сторонние поставщики ведения журнала](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="1f24b-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="1f24b-132">Создание журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-132">Create logs</span></span>

<span data-ttu-id="1f24b-133">Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601> из [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1f24b-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="1f24b-134">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1f24b-134">The following example:</span></span>

* <span data-ttu-id="1f24b-135">Создает средство ведения журнала `ILogger<AboutModel>`, которое использует *категорию* журналов с полным именем типа `AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="1f24b-136">Категория ведения журналов представляет строку, которая связана с каждым журналом.</span><span class="sxs-lookup"><span data-stu-id="1f24b-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="1f24b-137">Вызывает метод <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> для ведения журналов на уровне `Information`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="1f24b-138">*Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.</span><span class="sxs-lookup"><span data-stu-id="1f24b-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="1f24b-139">[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="1f24b-140">Дополнительные сведения о Blazor см. в статье [Создание журналов в Blazor и Blazor WebAssembly](#clib) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="1f24b-141">В разделах, посвященных [созданию журналов в классах Main и Startup](#clms), описывается создание журналов в `Main` и `Startup`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="1f24b-142">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-142">Configure logging</span></span>

<span data-ttu-id="1f24b-143">Конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файлах *appsettings*.`{Environment}` *.json*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="1f24b-144">Шаблоны веб-приложений ASP.NET Core создают следующий файл *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="1f24b-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="1f24b-145">В приведенном выше документе JSON:</span><span class="sxs-lookup"><span data-stu-id="1f24b-145">In the preceding JSON:</span></span>

* <span data-ttu-id="1f24b-146">Указаны категории `"Default"`, `"Microsoft"`и `"Microsoft.Hosting.Lifetime"`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="1f24b-147">Категория `"Microsoft"` применяется ко всем категориям, начинающимся с `"Microsoft"`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="1f24b-148">Например, этот параметр применяется к категории `"Microsoft.AspNetCore.Routing.EndpointMiddleware"`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="1f24b-149">Категория `"Microsoft"` задает ведение журналов на уровне `Warning` и более высоком.</span><span class="sxs-lookup"><span data-stu-id="1f24b-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="1f24b-150">Категория `"Microsoft.Hosting.Lifetime"` является более детальной по сравнению с `"Microsoft"`, поэтому при выборе категории `"Microsoft.Hosting.Lifetime"` ведение журналов осуществляется на уровне "Информация" и более высоком.</span><span class="sxs-lookup"><span data-stu-id="1f24b-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="1f24b-151">Поскольку конкретный поставщик журналов не указан, `LogLevel` применяется ко всем включенным поставщикам, за исключением [Windows EventLog](#welog).</span><span class="sxs-lookup"><span data-stu-id="1f24b-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="1f24b-152">Свойство `Logging` может иметь свойство <xref:Microsoft.Extensions.Logging.LogLevel> и свойства поставщика журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="1f24b-153">Свойство `LogLevel` указывает минимальный [уровень](#log-level) журнала для выбранных категорий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1f24b-154">В приведенном выше коде JSON заданы уровни ведения журнала `Information` и `Warning`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="1f24b-155">`LogLevel` определяет степень серьезности журнала и задается в диапазоне от 0 до 6:</span><span class="sxs-lookup"><span data-stu-id="1f24b-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="1f24b-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 и `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="1f24b-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="1f24b-157">Если задан `LogLevel`, журналы будут вестись для сообщений с указанным и более высокими уровнями.</span><span class="sxs-lookup"><span data-stu-id="1f24b-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="1f24b-158">В приведенном выше коде JSON задается ведение журналов для категории `Default` для сообщений с уровнем `Information` и более высоким.</span><span class="sxs-lookup"><span data-stu-id="1f24b-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="1f24b-159">Например, в журнал записываются сообщения с уровнями `Information`, `Warning`, `Error` и `Critical`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="1f24b-160">Если `LogLevel` не задан, по умолчанию устанавливается уровень ведения журналов `Information`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="1f24b-161">Дополнительные сведения см. в статье [Уровни ведения журналов](#llvl).</span><span class="sxs-lookup"><span data-stu-id="1f24b-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="1f24b-162">Свойство поставщика может задавать свойство `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="1f24b-163">Свойство `LogLevel` поставщика определяет уровень ведения журналов для этого поставщика и переопределяет любые другие не относящиеся к поставщику параметры ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="1f24b-164">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1f24b-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="1f24b-165">Параметры в `Logging.{providername}.LogLevel` переопределяют параметры в `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1f24b-166">В приведенном выше коде JSON для поставщика `Debug` задается уровень ведения журнала по умолчанию `Information`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="1f24b-167">Приведенный выше параметр задает уровень ведения журнала `Information` для всех категорий `Logging:Debug:`, за исключением `Microsoft.Hosting`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="1f24b-168">Если задана конкретная категория, она переопределяет категорию по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1f24b-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="1f24b-169">В приведенном выше коде JSON категории `Logging:Debug:LogLevel` `"Microsoft.Hosting"` и `"Default"` переопределяют параметры в `Logging:LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="1f24b-170">Минимальный уровень ведения журнала можно указать для:</span><span class="sxs-lookup"><span data-stu-id="1f24b-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="1f24b-171">конкретных поставщиков.  Например: `Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="1f24b-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="1f24b-172">конкретных категорий. Например: `Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="1f24b-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="1f24b-173">всех поставщиков и всех категорий: `Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="1f24b-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="1f24b-174">***Любые*** журналы с уровнем ниже минимального:</span><span class="sxs-lookup"><span data-stu-id="1f24b-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="1f24b-175">не передаются поставщику;</span><span class="sxs-lookup"><span data-stu-id="1f24b-175">Passed to the provider.</span></span>
* <span data-ttu-id="1f24b-176">не записываются в журнал и не отображаются.</span><span class="sxs-lookup"><span data-stu-id="1f24b-176">Logged or displayed.</span></span>

<span data-ttu-id="1f24b-177">Чтобы отключить все журналы, укажите [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="1f24b-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="1f24b-178">`LogLevel.None` имеет значение 6, то есть выше `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="1f24b-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="1f24b-179">Если поставщик поддерживает [области журналов](#logscopes), `IncludeScopes` определяет, включены ли они.</span><span class="sxs-lookup"><span data-stu-id="1f24b-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1f24b-180">Дополнительные сведения см. в статье [Области журналов](#logscopes).</span><span class="sxs-lookup"><span data-stu-id="1f24b-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="1f24b-181">Следующий файл *appsettings.json* содержит все поставщики, включенные по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1f24b-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="1f24b-182">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="1f24b-182">In the preceding sample:</span></span>

* <span data-ttu-id="1f24b-183">Категории и уровни не являются предлагаемыми значениями.</span><span class="sxs-lookup"><span data-stu-id="1f24b-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="1f24b-184">Этот пример представлен с целью продемонстрировать все поставщики по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="1f24b-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="1f24b-185">Параметры в `Logging.{providername}.LogLevel` переопределяют параметры в `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="1f24b-186">Например, уровень в `Debug.LogLevel.Default` переопределяет уровень в `LogLevel.Default`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="1f24b-187">Каждый поставщик по умолчанию использует *псевдоним*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="1f24b-188">Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа.</span><span class="sxs-lookup"><span data-stu-id="1f24b-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="1f24b-189">Ниже приведены псевдонимы встроенных поставщиков:</span><span class="sxs-lookup"><span data-stu-id="1f24b-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="1f24b-190">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-190">Console</span></span>
  * <span data-ttu-id="1f24b-191">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-191">Debug</span></span>
  * <span data-ttu-id="1f24b-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-192">EventSource</span></span>
  * <span data-ttu-id="1f24b-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-193">EventLog</span></span>
  * <span data-ttu-id="1f24b-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1f24b-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="1f24b-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1f24b-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="1f24b-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1f24b-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="1f24b-197">Настройка уровня ведения журнала с помощью командной строки, переменных среды и других способов конфигурации</span><span class="sxs-lookup"><span data-stu-id="1f24b-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="1f24b-198">Уровень ведения журнала может задаваться любыми [поставщиками конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1f24b-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1f24b-199">Приведенные ниже команды:</span><span class="sxs-lookup"><span data-stu-id="1f24b-199">The following commands:</span></span>

* <span data-ttu-id="1f24b-200">присваивают ключу среды `Logging:LogLevel:Microsoft` значение `Information` в Windows;</span><span class="sxs-lookup"><span data-stu-id="1f24b-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="1f24b-201">проверяют параметры при использовании приложения, созданного на основе шаблонов веб-приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="1f24b-202">Команда `dotnet run` должна выполняться в каталоге проекта после использования `set`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="1f24b-203">Приведенный выше параметр среды:</span><span class="sxs-lookup"><span data-stu-id="1f24b-203">The preceding environment setting:</span></span>

* <span data-ttu-id="1f24b-204">задается только в процессах, запускаемых из командного окна, в котором он был установлен;</span><span class="sxs-lookup"><span data-stu-id="1f24b-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="1f24b-205">не будет считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f24b-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="1f24b-206">Приведенная ниже команда [Setx](/windows-server/administration/windows-commands/setx) также задает ключ среды и значение в Windows.</span><span class="sxs-lookup"><span data-stu-id="1f24b-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="1f24b-207">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1f24b-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="1f24b-208">Параметр `/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="1f24b-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="1f24b-209">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="1f24b-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="1f24b-210">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="1f24b-211">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="1f24b-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="1f24b-212">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="1f24b-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="1f24b-213">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="1f24b-213">Exposed as environment variables.</span></span>

<span data-ttu-id="1f24b-214">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="1f24b-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="1f24b-215">Дополнительные сведения о настройке значений конфигурации ASP.NET Core с помощью переменных среды см. в статье [Переменные среды](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="1f24b-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="1f24b-216">Дополнительные сведения об использовании других источников конфигурации, включая командную строку, Azure Key Vault, службу конфигурации приложений Azure, различные форматы файлов и другие, см. в статье <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1f24b-217">Применение правил фильтрации</span><span class="sxs-lookup"><span data-stu-id="1f24b-217">How filtering rules are applied</span></span>

<span data-ttu-id="1f24b-218">При создании объекта <xref:Microsoft.Extensions.Logging.ILogger%601> объект <xref:Microsoft.Extensions.Logging.ILoggerFactory> выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1f24b-219">Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил.</span><span class="sxs-lookup"><span data-stu-id="1f24b-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1f24b-220">Самое подробное правило для каждой пары поставщика и категории выбирается из списка доступных правил.</span><span class="sxs-lookup"><span data-stu-id="1f24b-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1f24b-221">При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:</span><span class="sxs-lookup"><span data-stu-id="1f24b-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1f24b-222">Выберите все правила, которые соответствуют поставщику или его псевдониму.</span><span class="sxs-lookup"><span data-stu-id="1f24b-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1f24b-223">Если ничего не найдено, выберите все правила с пустым поставщиком.</span><span class="sxs-lookup"><span data-stu-id="1f24b-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1f24b-224">В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории.</span><span class="sxs-lookup"><span data-stu-id="1f24b-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1f24b-225">Если ничего не найдено, выберите все правила, которые не указывают категорию.</span><span class="sxs-lookup"><span data-stu-id="1f24b-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1f24b-226">Если выбрано несколько правил, примите **последнее**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1f24b-227">Если правила не выбраны, используйте `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="1f24b-228">Запись в журнал выходных данных dotnet run и Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f24b-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="1f24b-229">Журналы, созданные с помощью [установленных по умолчанию поставщиков ведения журнала](#lp), отображаются:</span><span class="sxs-lookup"><span data-stu-id="1f24b-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="1f24b-230">В Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f24b-230">In Visual Studio</span></span>
  * <span data-ttu-id="1f24b-231">в окне выходных данных отладки при отладке;</span><span class="sxs-lookup"><span data-stu-id="1f24b-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="1f24b-232">в окне веб-сервера ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="1f24b-233">В окне консоли при запуске приложения с помощью команды `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="1f24b-234">Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1f24b-235">В ASP.NET Core и коде приложения используются один и тот же API ведения журнала и одни и те же поставщики.</span><span class="sxs-lookup"><span data-stu-id="1f24b-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="1f24b-236">Категория журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-236">Log category</span></span>

<span data-ttu-id="1f24b-237">При создании объекта `ILogger` указывается *категория*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="1f24b-238">Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1f24b-239">Строка категории является необязательной, однако по соглашению следует использовать имя класса.</span><span class="sxs-lookup"><span data-stu-id="1f24b-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="1f24b-240">Например, в контроллере может использоваться имя `"TodoApi.Controllers.TodoController"`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="1f24b-241">Веб-приложения ASP.NET Core используют `ILogger<T>` для автоматического получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:</span><span class="sxs-lookup"><span data-stu-id="1f24b-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1f24b-242">Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="1f24b-243">Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="1f24b-244">Уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-244">Log level</span></span>

<span data-ttu-id="1f24b-245">В следующей таблице перечислены значения <xref:Microsoft.Extensions.Logging.LogLevel>, используемый для удобства метод расширения `Log{LogLevel}`, а также приводятся сведения о предполагаемом применении:</span><span class="sxs-lookup"><span data-stu-id="1f24b-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="1f24b-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="1f24b-246">LogLevel</span></span> | <span data-ttu-id="1f24b-247">Значение</span><span class="sxs-lookup"><span data-stu-id="1f24b-247">Value</span></span> | <span data-ttu-id="1f24b-248">Метод</span><span class="sxs-lookup"><span data-stu-id="1f24b-248">Method</span></span> | <span data-ttu-id="1f24b-249">Описание</span><span class="sxs-lookup"><span data-stu-id="1f24b-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="1f24b-250">Трассировка</span><span class="sxs-lookup"><span data-stu-id="1f24b-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-251">0</span><span class="sxs-lookup"><span data-stu-id="1f24b-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="1f24b-252">Содержит наиболее подробные сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-252">Contain the most detailed messages.</span></span> <span data-ttu-id="1f24b-253">Эти сообщения могут содержать конфиденциальные данные приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="1f24b-254">Эти сообщения по умолчанию отключены, и их ***никогда*** не следует включать в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1f24b-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="1f24b-255">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-256">1</span><span class="sxs-lookup"><span data-stu-id="1f24b-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="1f24b-257">Используется для отладки и разработки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-257">For debugging and development.</span></span> <span data-ttu-id="1f24b-258">В рабочей среде следует использовать с осторожностью из-за большого объема.</span><span class="sxs-lookup"><span data-stu-id="1f24b-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="1f24b-259">Информация</span><span class="sxs-lookup"><span data-stu-id="1f24b-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-260">2</span><span class="sxs-lookup"><span data-stu-id="1f24b-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="1f24b-261">Отслеживание общего потока работы приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="1f24b-262">Может использоваться в долгосрочных целях.</span><span class="sxs-lookup"><span data-stu-id="1f24b-262">May have long-term value.</span></span> |
| [<span data-ttu-id="1f24b-263">Предупреждение</span><span class="sxs-lookup"><span data-stu-id="1f24b-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-264">3</span><span class="sxs-lookup"><span data-stu-id="1f24b-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="1f24b-265">Для нестандартных или непредвиденных событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="1f24b-266">Обычно содержит ошибки или условия, которые не приводят к сбою приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="1f24b-267">Ошибка</span><span class="sxs-lookup"><span data-stu-id="1f24b-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-268">4</span><span class="sxs-lookup"><span data-stu-id="1f24b-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="1f24b-269">Для ошибок и исключений, которые не могут быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="1f24b-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1f24b-270">Эти сообщения указывают на сбой текущих операции или запроса, а не на ошибку уровня приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="1f24b-271">Критическая</span><span class="sxs-lookup"><span data-stu-id="1f24b-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-272">5</span><span class="sxs-lookup"><span data-stu-id="1f24b-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="1f24b-273">Для сбоев, которые требуют немедленного внимания.</span><span class="sxs-lookup"><span data-stu-id="1f24b-273">For failures that require immediate attention.</span></span> <span data-ttu-id="1f24b-274">Примеры: потеря данных, нехватка места на диске.</span><span class="sxs-lookup"><span data-stu-id="1f24b-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="1f24b-275">None</span><span class="sxs-lookup"><span data-stu-id="1f24b-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="1f24b-276">6</span><span class="sxs-lookup"><span data-stu-id="1f24b-276">6</span></span> | | <span data-ttu-id="1f24b-277">Указывает, что категория ведения журнала не должна подразумевать запись каких-либо сообщений.</span><span class="sxs-lookup"><span data-stu-id="1f24b-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="1f24b-278">В приведенной выше таблице значения `LogLevel` приведены в порядке от самого низкого к самому высокому уровню серьезности.</span><span class="sxs-lookup"><span data-stu-id="1f24b-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="1f24b-279">Первый параметр метода [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions), <xref:Microsoft.Extensions.Logging.LogLevel>, указывает на степень серьезности журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="1f24b-280">Вместо вызова `Log(LogLevel, ...)` большинство разработчиков вызывают методы расширения [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="1f24b-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="1f24b-281">Методы расширения `Log{LogLevel}` [вызывают метод Log и задают значение LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="1f24b-282">Например, следующие два вызова ведения журнала функционально эквивалентны и позволяют получить одинаковые журналы:</span><span class="sxs-lookup"><span data-stu-id="1f24b-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="1f24b-283">`MyLogEvents.TestItem` — это идентификатор события.</span><span class="sxs-lookup"><span data-stu-id="1f24b-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="1f24b-284">`MyLogEvents` является частью примера приложения и отображается в разделе [Идентификатор события журнала](#leid).</span><span class="sxs-lookup"><span data-stu-id="1f24b-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1f24b-285">Следующий код создает журналы `Information`и `Warning`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1f24b-286">В коде выше первый параметр, `MyLogEvents.GetItem`, — это `Log{LogLevel}`[идентификатор события журнала](#leid).</span><span class="sxs-lookup"><span data-stu-id="1f24b-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="1f24b-287">Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1f24b-288">Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#lmt), далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="1f24b-289">С помощью вызова соответствующего метода `Log{LogLevel}` можно управлять объемом выходных данных, записываемых на определенный носитель.</span><span class="sxs-lookup"><span data-stu-id="1f24b-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="1f24b-290">Пример:</span><span class="sxs-lookup"><span data-stu-id="1f24b-290">For example:</span></span>

* <span data-ttu-id="1f24b-291">В рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="1f24b-291">In production:</span></span>
  * <span data-ttu-id="1f24b-292">При ведении журнала на уровнях `Trace` или `Information` создается большой объем подробных сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1f24b-293">Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях `Trace` и `Information` в хранилище данных с низкими затратами и большим объемом.</span><span class="sxs-lookup"><span data-stu-id="1f24b-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="1f24b-294">Рассмотрите возможность ограничения уровней `Trace` и `Information` конкретными категориями.</span><span class="sxs-lookup"><span data-stu-id="1f24b-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="1f24b-295">При ведении журналов на уровнях с `Warning` по `Critical` создается немного сообщений.</span><span class="sxs-lookup"><span data-stu-id="1f24b-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="1f24b-296">При этом стоимость и ограничения хранения, как правило, не важны.</span><span class="sxs-lookup"><span data-stu-id="1f24b-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="1f24b-297">Меньший объем журналов позволяет выбирать среди большего количества вариантов хранения данных.</span><span class="sxs-lookup"><span data-stu-id="1f24b-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="1f24b-298">В среде разработки:</span><span class="sxs-lookup"><span data-stu-id="1f24b-298">In development:</span></span>
  * <span data-ttu-id="1f24b-299">Задайте значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="1f24b-300">Добавляйте сообщения уровней `Trace` или `Information` при устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="1f24b-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="1f24b-301">Чтобы ограничить объем выходных данных, задавайте уровни `Trace` или `Information` только для исследуемых категорий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="1f24b-302">ASP.NET Core создает журналы для событий платформы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1f24b-303">В качестве примера рассмотрим выходные данные журнала для:</span><span class="sxs-lookup"><span data-stu-id="1f24b-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="1f24b-304">приложения страниц Razor, созданного на основе шаблонов ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="1f24b-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="1f24b-305">уровня ведения журналов `Logging:Console:LogLevel:Microsoft:Information`;</span><span class="sxs-lookup"><span data-stu-id="1f24b-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="1f24b-306">перехода на страницу сведений о конфиденциальности:</span><span class="sxs-lookup"><span data-stu-id="1f24b-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="1f24b-307">Приведенный ниже код JSON задает уровень `Logging:Console:LogLevel:Microsoft:Information`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="1f24b-308">Идентификатор события журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-308">Log event ID</span></span>

<span data-ttu-id="1f24b-309">Каждый журнал может указывать *идентификатор события*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1f24b-310">В этом примере приложения используется класс `MyLogEvents` для определения идентификаторов событий:</span><span class="sxs-lookup"><span data-stu-id="1f24b-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1f24b-311">Идентификатор события связывает набор событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-311">An event ID associates a set of events.</span></span> <span data-ttu-id="1f24b-312">Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.</span><span class="sxs-lookup"><span data-stu-id="1f24b-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1f24b-313">Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить.</span><span class="sxs-lookup"><span data-stu-id="1f24b-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1f24b-314">Поставщик Debug не отображает идентификаторы событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1f24b-315">Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:</span><span class="sxs-lookup"><span data-stu-id="1f24b-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="1f24b-316">Некоторые поставщики ведения журнала хранят идентификатор события в поле, что позволяет выполнять фильтрацию по идентификатору.</span><span class="sxs-lookup"><span data-stu-id="1f24b-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="1f24b-317">Шаблон сообщения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="1f24b-318">Каждый API ведения журнала использует шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-318">Each log API uses a message template.</span></span> <span data-ttu-id="1f24b-319">Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1f24b-320">Используйте для заполнителей имена, а не числа.</span><span class="sxs-lookup"><span data-stu-id="1f24b-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="1f24b-321">Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами.</span><span class="sxs-lookup"><span data-stu-id="1f24b-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1f24b-322">В приведенном ниже коде имена параметров идут не по порядку в шаблоне сообщения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1f24b-323">Приведенный выше код создает сообщение журнала со значениями параметров в определенном порядке:</span><span class="sxs-lookup"><span data-stu-id="1f24b-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="1f24b-324">Такой подход позволяет поставщикам ведения журнала реализовывать [семантическое или структурированное ведение журналов](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="1f24b-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="1f24b-325">Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1f24b-326">Это позволяет поставщикам ведения журнала хранить значения параметров как поля.</span><span class="sxs-lookup"><span data-stu-id="1f24b-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1f24b-327">Например, рассмотрим следующий метод средства ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="1f24b-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1f24b-328">Например, при ведении журнала в хранилище таблиц Azure:</span><span class="sxs-lookup"><span data-stu-id="1f24b-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="1f24b-329">каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`;</span><span class="sxs-lookup"><span data-stu-id="1f24b-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="1f24b-330">использование таблиц со свойствами позволяет упростить выполнение запросов к данным журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="1f24b-331">Например, с помощью запроса можно находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="1f24b-332">Запись исключений в журнал</span><span class="sxs-lookup"><span data-stu-id="1f24b-332">Log exceptions</span></span>

<span data-ttu-id="1f24b-333">Методы средства ведения журнала имеют перегрузки, которые принимают параметр исключения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="1f24b-334">Принципы записи исключений в журнал зависят от конкретного поставщика.</span><span class="sxs-lookup"><span data-stu-id="1f24b-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="1f24b-335">Уровень ведения журнала по умолчанию</span><span class="sxs-lookup"><span data-stu-id="1f24b-335">Default log level</span></span>

<span data-ttu-id="1f24b-336">Если не задан уровень ведения журнала по умолчанию, то по умолчанию используется уровень `Information`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="1f24b-337">Рассмотрим в качестве примера следующее веб-приложение:</span><span class="sxs-lookup"><span data-stu-id="1f24b-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="1f24b-338">приложение создано на основе шаблонов веб-приложений ASP.NET;</span><span class="sxs-lookup"><span data-stu-id="1f24b-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="1f24b-339">файлы *appsettings.json* и *appsettings.Development.json* были удалены или переименованы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="1f24b-340">В рамках приведенной выше конфигурации при переходе на страницу сведений о конфиденциальности или домашнюю страницу создается множество сообщений с уровнем `Trace`, `Debug` и `Information`, в имени категории которых используется `Microsoft`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="1f24b-341">В следующем коде задается уровень ведения журнала по умолчанию в том случае, если этот уровень не определен в конфигурации:</span><span class="sxs-lookup"><span data-stu-id="1f24b-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="1f24b-342">Как правило, уровни ведения журнала следует задавать не в коде, а в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1f24b-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="1f24b-343">Функция фильтрации</span><span class="sxs-lookup"><span data-stu-id="1f24b-343">Filter function</span></span>

<span data-ttu-id="1f24b-344">Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила:</span><span class="sxs-lookup"><span data-stu-id="1f24b-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="1f24b-345">Приведенный выше код отображает журналы консоли, если категория содержит `Controller` или `Microsoft` и задан уровень ведения журнала `Information` или выше.</span><span class="sxs-lookup"><span data-stu-id="1f24b-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="1f24b-346">Как правило, уровни ведения журнала следует задавать не в коде, а в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1f24b-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="1f24b-347">Категории ASP.NET Core и EF Core</span><span class="sxs-lookup"><span data-stu-id="1f24b-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="1f24b-348">В следующей таблице приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:</span><span class="sxs-lookup"><span data-stu-id="1f24b-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="1f24b-349">Категория</span><span class="sxs-lookup"><span data-stu-id="1f24b-349">Category</span></span>                            | <span data-ttu-id="1f24b-350">Примечания</span><span class="sxs-lookup"><span data-stu-id="1f24b-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1f24b-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1f24b-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1f24b-352">Общая диагностика ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1f24b-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1f24b-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1f24b-354">Распознанные, найденные и использованные ключи.</span><span class="sxs-lookup"><span data-stu-id="1f24b-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1f24b-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1f24b-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1f24b-356">Разрешенные узлы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-356">Hosts allowed.</span></span> |
| <span data-ttu-id="1f24b-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1f24b-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1f24b-358">Время начала и длительность выполнения HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1f24b-359">Определение загруженных начальных сборок размещения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1f24b-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1f24b-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1f24b-361">Диагностика MVC и Razor.</span><span class="sxs-lookup"><span data-stu-id="1f24b-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1f24b-362">Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия.</span><span class="sxs-lookup"><span data-stu-id="1f24b-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1f24b-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1f24b-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1f24b-364">Перенаправление соответствующих сведений.</span><span class="sxs-lookup"><span data-stu-id="1f24b-364">Route matching information.</span></span> |
| <span data-ttu-id="1f24b-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1f24b-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1f24b-366">Запуск, остановка и сохранение ответов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1f24b-367">Сведения о сертификате HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1f24b-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1f24b-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1f24b-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1f24b-369">Обработанные файлы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-369">Files served.</span></span> |
| <span data-ttu-id="1f24b-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1f24b-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1f24b-371">Общая диагностика Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1f24b-372">Действия и конфигурация базы данных, обнаружение изменений, переносы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="1f24b-373">Чтобы просмотреть в окне консоли другие категории, выполните следующее присвоение **appsettings.Development.json**:</span><span class="sxs-lookup"><span data-stu-id="1f24b-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="1f24b-374">Области журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-374">Log scopes</span></span>

 <span data-ttu-id="1f24b-375">*Область* может группировать набор логических операций.</span><span class="sxs-lookup"><span data-stu-id="1f24b-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1f24b-376">Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора.</span><span class="sxs-lookup"><span data-stu-id="1f24b-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1f24b-377">Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.</span><span class="sxs-lookup"><span data-stu-id="1f24b-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1f24b-378">Область:</span><span class="sxs-lookup"><span data-stu-id="1f24b-378">A scope:</span></span>

* <span data-ttu-id="1f24b-379">имеет тип <xref:System.IDisposable>, который возвращается методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>;</span><span class="sxs-lookup"><span data-stu-id="1f24b-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="1f24b-380">действует вплоть до удаления.</span><span class="sxs-lookup"><span data-stu-id="1f24b-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="1f24b-381">Области поддерживаются следующими поставщиками:</span><span class="sxs-lookup"><span data-stu-id="1f24b-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="1f24b-382">AzureAppServicesFile и AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1f24b-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="1f24b-383">Используйте область, заключив вызовы средства ведения журналов в блок `using`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="1f24b-384">Следующий код JSON предоставляет области для поставщика Console:</span><span class="sxs-lookup"><span data-stu-id="1f24b-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="1f24b-385">Следующий код предоставляет области для поставщика Console:</span><span class="sxs-lookup"><span data-stu-id="1f24b-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="1f24b-386">Как правило, параметры ведения журнала следует задавать не в коде, а в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1f24b-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="1f24b-387">Встроенные поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-387">Built-in logging providers</span></span>

<span data-ttu-id="1f24b-388">ASP.NET Core включает следующие поставщики ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="1f24b-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="1f24b-389">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-389">Console</span></span>](#console)
* [<span data-ttu-id="1f24b-390">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-390">Debug</span></span>](#debug)
* [<span data-ttu-id="1f24b-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="1f24b-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="1f24b-393">AzureAppServicesFile и AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1f24b-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="1f24b-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1f24b-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="1f24b-395">Дополнительные сведения о `stdout` и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="1f24b-396">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-396">Console</span></span>

<span data-ttu-id="1f24b-397">Поставщик `Console` выводит выходные данные в консоль.</span><span class="sxs-lookup"><span data-stu-id="1f24b-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="1f24b-398">Дополнительные сведения о просмотре журналов `Console` в среде разработки см. в статье [Запись в журнал выходных данных dotnet run и Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="1f24b-399">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-399">Debug</span></span>

<span data-ttu-id="1f24b-400">Поставщик `Debug` записывает выходные данные журнала с использованием класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug).</span><span class="sxs-lookup"><span data-stu-id="1f24b-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="1f24b-401">При вызове методов `System.Diagnostics.Debug.WriteLine` выполняется запись в поставщик `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="1f24b-402">В Linux расположение журнала поставщика `Debug` зависит от дистрибутива и может быть одним из следующих:</span><span class="sxs-lookup"><span data-stu-id="1f24b-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="1f24b-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="1f24b-403">*/var/log/message*</span></span>
* <span data-ttu-id="1f24b-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="1f24b-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="1f24b-405">Источник события</span><span class="sxs-lookup"><span data-stu-id="1f24b-405">Event Source</span></span>

<span data-ttu-id="1f24b-406">Поставщик `EventSource` выполняет запись в кроссплатформенный источник событий с именем `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1f24b-407">В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="1f24b-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="1f24b-408">Средства трассировки dotnet</span><span class="sxs-lookup"><span data-stu-id="1f24b-408">dotnet trace tooling</span></span>

<span data-ttu-id="1f24b-409">Средство [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) — это универсальное кроссплатформенное средство командной строки, которое выполняет сбор трассировок .NET Core для запущенного процесса.</span><span class="sxs-lookup"><span data-stu-id="1f24b-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="1f24b-410">Средство собирает данные поставщика <xref:Microsoft.Extensions.Logging.EventSource> с помощью <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="1f24b-411">Инструкции по установке см. в статье [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace).</span><span class="sxs-lookup"><span data-stu-id="1f24b-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="1f24b-412">Используйте средства трассировки dotnet, чтобы получить трассировку из приложения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="1f24b-413">Запустите приложение с помощью команды `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="1f24b-414">Определите идентификатор процесса (PID) приложения .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f24b-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="1f24b-415">В Windows воспользуйтесь одним из перечисленных ниже подходов:</span><span class="sxs-lookup"><span data-stu-id="1f24b-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="1f24b-416">Диспетчер задач (CTRL+ALT+DEL)</span><span class="sxs-lookup"><span data-stu-id="1f24b-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="1f24b-417">Команда tasklist</span><span class="sxs-lookup"><span data-stu-id="1f24b-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="1f24b-418">Команда Powershell Get-Process</span><span class="sxs-lookup"><span data-stu-id="1f24b-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="1f24b-419">В Linux используйте [команду pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="1f24b-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="1f24b-420">Найдите идентификатор процесса, имя которого совпадает с именем сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="1f24b-421">Выполните команду `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="1f24b-422">Общий синтаксис команды:</span><span class="sxs-lookup"><span data-stu-id="1f24b-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="1f24b-423">При использовании командной оболочки PowerShell заключите значение `--providers` в одинарные кавычки (`'`):</span><span class="sxs-lookup"><span data-stu-id="1f24b-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="1f24b-424">На платформах, отличных от Windows, добавьте параметр `-f speedscope`, чтобы изменить формат выходного файла трассировки на `speedscope`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="1f24b-425">Ключевое слово</span><span class="sxs-lookup"><span data-stu-id="1f24b-425">Keyword</span></span> | <span data-ttu-id="1f24b-426">Описание</span><span class="sxs-lookup"><span data-stu-id="1f24b-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="1f24b-427">1</span><span class="sxs-lookup"><span data-stu-id="1f24b-427">1</span></span>       | <span data-ttu-id="1f24b-428">Занесите в журнал метасобытия о `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="1f24b-429">Не заносит в журнал события из `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="1f24b-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="1f24b-430">2</span><span class="sxs-lookup"><span data-stu-id="1f24b-430">2</span></span>       | <span data-ttu-id="1f24b-431">Включает событие `Message` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1f24b-432">Предоставляет сведения в исходном виде (без форматирования).</span><span class="sxs-lookup"><span data-stu-id="1f24b-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="1f24b-433">4</span><span class="sxs-lookup"><span data-stu-id="1f24b-433">4</span></span>       | <span data-ttu-id="1f24b-434">Включает событие `FormatMessage` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1f24b-435">Предоставляет сведения в виде отформатированной строки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="1f24b-436">8</span><span class="sxs-lookup"><span data-stu-id="1f24b-436">8</span></span>       | <span data-ttu-id="1f24b-437">Включает событие `MessageJson` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1f24b-438">Предоставляет представление аргументов в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="1f24b-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="1f24b-439">Уровень события</span><span class="sxs-lookup"><span data-stu-id="1f24b-439">Event Level</span></span> | <span data-ttu-id="1f24b-440">Описание</span><span class="sxs-lookup"><span data-stu-id="1f24b-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="1f24b-441">0</span><span class="sxs-lookup"><span data-stu-id="1f24b-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="1f24b-442">1</span><span class="sxs-lookup"><span data-stu-id="1f24b-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="1f24b-443">2</span><span class="sxs-lookup"><span data-stu-id="1f24b-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="1f24b-444">3</span><span class="sxs-lookup"><span data-stu-id="1f24b-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="1f24b-445">4</span><span class="sxs-lookup"><span data-stu-id="1f24b-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="1f24b-446">5</span><span class="sxs-lookup"><span data-stu-id="1f24b-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="1f24b-447">Записи `FilterSpecs` для `{Logger Category}` и `{Event Level}` представляют дополнительные условия фильтрации журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="1f24b-448">Отдельные записи `FilterSpecs` разделяются точкой с запятой (`;`).</span><span class="sxs-lookup"><span data-stu-id="1f24b-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="1f24b-449">Пример использования командной оболочки Windows (**не** одинарные кавычки вокруг значения `--providers`):</span><span class="sxs-lookup"><span data-stu-id="1f24b-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="1f24b-450">Предыдущая команда активирует:</span><span class="sxs-lookup"><span data-stu-id="1f24b-450">The preceding command activates:</span></span>

   * <span data-ttu-id="1f24b-451">Средство ведения журнала источника событий для создания форматированных строк (`4`) для ошибок (`2`).</span><span class="sxs-lookup"><span data-stu-id="1f24b-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="1f24b-452">Ведение журнала `Microsoft.AspNetCore.Hosting` на уровне ведения журнала `Informational` (`4`).</span><span class="sxs-lookup"><span data-stu-id="1f24b-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="1f24b-453">Остановите средства трассировки dotnet, нажав клавишу ENTER или CTRL+C.</span><span class="sxs-lookup"><span data-stu-id="1f24b-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="1f24b-454">Трассировка сохраняется с именем *trace.nettrace* в папке, в которой выполняется команда `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="1f24b-455">Откройте трассировку с помощью [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="1f24b-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="1f24b-456">Откройте файл *trace.nettrace* и изучите события трассировки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="1f24b-457">Если приложение не создает узел с `CreateDefaultBuilder`, добавьте [Поставщика источника событий](#event-source-provider) в конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="1f24b-458">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="1f24b-458">For more information, see:</span></span>

* <span data-ttu-id="1f24b-459">[Трассировка для программы анализа производительности (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (документация по .NET Core)</span><span class="sxs-lookup"><span data-stu-id="1f24b-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="1f24b-460">[Трассировка для программы анализа производительности (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (документация по репозиторию GitHub dotnet/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="1f24b-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="1f24b-461">[Класс LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (обозреватель API .NET)</span><span class="sxs-lookup"><span data-stu-id="1f24b-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="1f24b-462">[Источник ссылки LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): чтобы получить источник ссылки для другой версии, измените ветку на `release/{Version}`, где `{Version}` — это нужная версия ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="1f24b-463">[Perfview](#perfview): полезный инструмент для просмотра трассировок источника событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="1f24b-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="1f24b-464">Perfview</span></span>

<span data-ttu-id="1f24b-465">Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="1f24b-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1f24b-466">Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1f24b-467">Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1f24b-468">Не пропустите символ `*` в начале строки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="1f24b-469">Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-469">Windows EventLog</span></span>

<span data-ttu-id="1f24b-470">Поставщик `EventLog` отправляет выходные данные журнала в журнал событий Windows.</span><span class="sxs-lookup"><span data-stu-id="1f24b-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="1f24b-471">В отличие от других поставщиков, поставщик `EventLog` ***не наследует*** параметры по умолчанию, не относящиеся к поставщику.</span><span class="sxs-lookup"><span data-stu-id="1f24b-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="1f24b-472">Если параметры журнала `EventLog` не указаны, [по умолчанию используется уровень LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="1f24b-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="1f24b-473">Чтобы регистрировать события с уровнем ниже <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, следует явно задать уровень ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="1f24b-474">В следующем примере для журнала событий по умолчанию задается уровень <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="1f24b-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="1f24b-475">[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1f24b-476">Если `null` или не указан, используются следующие параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1f24b-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1f24b-477">`LogName`. "Application"</span><span class="sxs-lookup"><span data-stu-id="1f24b-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="1f24b-478">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="1f24b-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1f24b-479">`MachineName`. Используется имя локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="1f24b-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1f24b-480">Следующий код изменяет `SourceName` со значения по умолчанию `".NET Runtime"` на `MyLogs`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="1f24b-481">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-481">Azure App Service</span></span>

<span data-ttu-id="1f24b-482">Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="1f24b-483">Пакет поставщика не включен в общую платформу.</span><span class="sxs-lookup"><span data-stu-id="1f24b-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="1f24b-484">Чтобы использовать поставщик, добавьте пакет поставщика в проект.</span><span class="sxs-lookup"><span data-stu-id="1f24b-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="1f24b-485">Для настройки параметров поставщика используйте <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> и <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1f24b-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="1f24b-486">При развертывании в службе приложений Azure ваше приложение использует параметры в разделе [Журналы службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) на странице **Служба приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1f24b-487">При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1f24b-488">**Ведение журнала приложения (файловая система)** ;</span><span class="sxs-lookup"><span data-stu-id="1f24b-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1f24b-489">**Ведение журнала приложения (BLOB-объект)** .</span><span class="sxs-lookup"><span data-stu-id="1f24b-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1f24b-490">По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1f24b-491">Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2.</span><span class="sxs-lookup"><span data-stu-id="1f24b-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1f24b-492">Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1f24b-493">Этот поставщик работает только при выполнении проекта в среде Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1f24b-494">Потоковая передача журналов Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-494">Azure log streaming</span></span>

<span data-ttu-id="1f24b-495">Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="1f24b-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="1f24b-496">сервер приложений;</span><span class="sxs-lookup"><span data-stu-id="1f24b-496">The app server</span></span>
* <span data-ttu-id="1f24b-497">веб-сервер;</span><span class="sxs-lookup"><span data-stu-id="1f24b-497">The web server</span></span>
* <span data-ttu-id="1f24b-498">трассировка неудачно завершенных запросов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-498">Failed request tracing</span></span>

<span data-ttu-id="1f24b-499">Настройка потоковой передачи журналов Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1f24b-500">Со страницы портала приложения перейдите на страницу **Журналы службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="1f24b-501">**Включите** параметр **Ведение журнала приложения (файловая система)** .</span><span class="sxs-lookup"><span data-stu-id="1f24b-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1f24b-502">Выберите **уровень** ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-502">Choose the log **Level**.</span></span> <span data-ttu-id="1f24b-503">Этот параметр применяется только к потоковой передаче журналов Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="1f24b-504">Перейдите на страницу **Поток журналов**, чтобы просмотреть журналы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="1f24b-505">Сообщения записываются в журнал с помощью интерфейса `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="1f24b-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1f24b-506">Azure Application Insights</span></span>

<span data-ttu-id="1f24b-507">Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="1f24b-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="1f24b-508">Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии.</span><span class="sxs-lookup"><span data-stu-id="1f24b-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1f24b-509">Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1f24b-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1f24b-510">Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1f24b-511">Если вы используете этот пакет, пакет поставщика устанавливать не нужно.</span><span class="sxs-lookup"><span data-stu-id="1f24b-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1f24b-512">Пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) предназначен для ASP.NET 4.x, а не для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="1f24b-513">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="1f24b-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1f24b-514">Общие сведения об Application Insights</span><span class="sxs-lookup"><span data-stu-id="1f24b-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1f24b-515">[Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1f24b-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.</span><span class="sxs-lookup"><span data-stu-id="1f24b-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1f24b-517">[Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1f24b-518">[Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="1f24b-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1f24b-519">Сторонние поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-519">Third-party logging providers</span></span>

<span data-ttu-id="1f24b-520">Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f24b-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1f24b-521">[ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1f24b-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1f24b-523">[JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));</span><span class="sxs-lookup"><span data-stu-id="1f24b-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1f24b-524">[KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1f24b-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1f24b-525">[Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1f24b-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1f24b-526">[Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-527">[NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([в репозитории GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="1f24b-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="1f24b-529">[Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1f24b-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1f24b-530">[Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).</span><span class="sxs-lookup"><span data-stu-id="1f24b-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1f24b-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1f24b-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1f24b-532">Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1f24b-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1f24b-533">Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:</span><span class="sxs-lookup"><span data-stu-id="1f24b-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1f24b-534">Добавьте пакет NuGet в проект.</span><span class="sxs-lookup"><span data-stu-id="1f24b-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1f24b-535">Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1f24b-536">Дополнительные сведения см. в документации по каждому поставщику.</span><span class="sxs-lookup"><span data-stu-id="1f24b-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1f24b-537">Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="1f24b-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="1f24b-538">Консольные приложения без размещения</span><span class="sxs-lookup"><span data-stu-id="1f24b-538">Non-host console app</span></span>

<span data-ttu-id="1f24b-539">Пример использования универсального узла в приложении, не являющемся веб-консолью, см. в файле *Program.cs* [примера приложения "Фоновые задачи"](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="1f24b-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="1f24b-540">Код ведения журнала для приложений без универсального узла отличается тем, как [добавляются поставщики](#add-providers) и [создаются средства ведения журнала](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="1f24b-541">Поставщики ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-541">Logging providers</span></span>

<span data-ttu-id="1f24b-542">В консольном приложении, не использующем узел, вызовите метод расширения `Add{provider name}` поставщика при создании `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="1f24b-543">Создание журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-543">Create logs</span></span>

<span data-ttu-id="1f24b-544">Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1f24b-545">Используйте `LoggerFactory`, чтобы создать `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1f24b-546">В приведенном ниже примере для создается средство ведения журнала с категорией `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="1f24b-547">В приведенных ниже примерах ASP.NET CORE средство ведения журнала используется для создания журналов с уровнем `Information`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1f24b-548">*Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.</span><span class="sxs-lookup"><span data-stu-id="1f24b-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="1f24b-549">[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="1f24b-550">Ведение журнала во время создания узла</span><span class="sxs-lookup"><span data-stu-id="1f24b-550">Log during host construction</span></span>

<span data-ttu-id="1f24b-551">Ведение журнала во время создания узла не поддерживается напрямую.</span><span class="sxs-lookup"><span data-stu-id="1f24b-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1f24b-552">Однако можно использовать отдельное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-552">However, a separate logger can be used.</span></span> <span data-ttu-id="1f24b-553">В следующем примере для входа в `CreateHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/).</span><span class="sxs-lookup"><span data-stu-id="1f24b-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="1f24b-554">`AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="1f24b-555">Настройка службы, зависящей от ILogger</span><span class="sxs-lookup"><span data-stu-id="1f24b-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="1f24b-556">Внедрение средства ведения журнала в класс `Startup` посредством конструктора работает в более ранних версиях ASP.NET Core, так как для веб-узла создается отдельный контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1f24b-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="1f24b-557">Сведения о том, почему для универсального узла создается только один контейнер, см. в [объявлении о критическом изменении](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="1f24b-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="1f24b-558">Чтобы настроить службу, зависящую от `ILogger<T>`, используйте внедрение конструктора или предоставьте фабричный метод.</span><span class="sxs-lookup"><span data-stu-id="1f24b-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="1f24b-559">Фабричный метод рекомендуется использовать, только если нет других вариантов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="1f24b-560">Например, рассмотрим службу, которой требуется экземпляр `ILogger<T>`, предоставляемый путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="1f24b-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="1f24b-561">Выделенный выше код — это функция [Func](/dotnet/api/system.func-2), которая выполняется, когда контейнеру внедрения зависимостей впервые требуется создать экземпляр `MyService`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="1f24b-562">Таким образом можно обращаться к любым зарегистрированным службам.</span><span class="sxs-lookup"><span data-stu-id="1f24b-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="1f24b-563">Создание журналов в классе Main</span><span class="sxs-lookup"><span data-stu-id="1f24b-563">Create logs in Main</span></span>

<span data-ttu-id="1f24b-564">Следующий код создает журналы в `Main`, получая экземпляр `ILogger` путем внедрения зависимостей после создания узла:</span><span class="sxs-lookup"><span data-stu-id="1f24b-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="1f24b-565">Создание журналов в классе Startup</span><span class="sxs-lookup"><span data-stu-id="1f24b-565">Create logs in Startup</span></span>

<span data-ttu-id="1f24b-566">Следующий код записывает журналы в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="1f24b-567">Запись журналов до завершения настройки контейнера внедрения зависимостей в методе `Startup.ConfigureServices` не поддерживается:</span><span class="sxs-lookup"><span data-stu-id="1f24b-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="1f24b-568">Внедрение средства ведения журнала в конструктор `Startup` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="1f24b-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="1f24b-569">Внедрение средства ведения журнала в сигнатуру метода `Startup.ConfigureServices` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="1f24b-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="1f24b-570">Причина этого ограничения заключается в том, что ведение журнала зависит от внедрения зависимостей и от конфигурации, которая, в свою очередь, зависит от внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1f24b-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="1f24b-571">Контейнер внедрения зависимостей не настраивается, пока не завершится выполнение `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="1f24b-572">Дополнительные сведения о настройке службы, которая зависит от `ILogger<T>`, а также о причинах, по которым внедрение конструктора для средства ведения журнала в `Startup` работало в более ранних версиях, см. в разделе [Настройка службы, зависящей от ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="1f24b-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1f24b-573">Асинхронные методы ведения журналов не выполняются</span><span class="sxs-lookup"><span data-stu-id="1f24b-573">No asynchronous logger methods</span></span>

<span data-ttu-id="1f24b-574">Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1f24b-575">Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище,</span><span class="sxs-lookup"><span data-stu-id="1f24b-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1f24b-576">а затем перемещайте их в медленное хранилище.</span><span class="sxs-lookup"><span data-stu-id="1f24b-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="1f24b-577">Например, если вы записываете журналы в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными.</span><span class="sxs-lookup"><span data-stu-id="1f24b-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1f24b-578">Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f24b-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1f24b-579">Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="1f24b-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="1f24b-580">Изменение уровней ведения журнала в работающем приложении</span><span class="sxs-lookup"><span data-stu-id="1f24b-580">Change log levels in a running app</span></span>

<span data-ttu-id="1f24b-581">API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1f24b-582">Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1f24b-583">Например, [поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider) по умолчанию перезагружает конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="1f24b-584">Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="1f24b-585">ILogger и ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="1f24b-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="1f24b-586">Интерфейсы <xref:Microsoft.Extensions.Logging.ILogger%601> и <xref:Microsoft.Extensions.Logging.ILoggerFactory>, а также их реализации включены в пакет SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="1f24b-587">Кроме того, они доступны в следующих пакетах NuGet:</span><span class="sxs-lookup"><span data-stu-id="1f24b-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="1f24b-588">Эти интерфейсы находятся в пространстве имен [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="1f24b-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="1f24b-589">Их реализации по умолчанию находятся в пакете [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="1f24b-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="1f24b-590">Применение правил фильтрации журналов в коде</span><span class="sxs-lookup"><span data-stu-id="1f24b-590">Apply log filter rules in code</span></span>

<span data-ttu-id="1f24b-591">Для настройки правил фильтрации журналов рекомендуется использовать [конфигурацию](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1f24b-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="1f24b-592">В следующем примере показано, как зарегистрировать в коде правила фильтрации:</span><span class="sxs-lookup"><span data-stu-id="1f24b-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="1f24b-593">`logging.AddFilter("System", LogLevel.Debug)` задает категорию `System` и уровень ведения журнала `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="1f24b-594">Поскольку конкретный поставщик не задан, фильтр применяется ко всем поставщикам.</span><span class="sxs-lookup"><span data-stu-id="1f24b-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="1f24b-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` задает:</span><span class="sxs-lookup"><span data-stu-id="1f24b-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="1f24b-596">поставщик ведения журнала `Debug`;</span><span class="sxs-lookup"><span data-stu-id="1f24b-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="1f24b-597">уровень ведения журнала `Information` и выше;</span><span class="sxs-lookup"><span data-stu-id="1f24b-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="1f24b-598">все категории, начинающиеся с `"Microsoft"`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="1f24b-599">Создание пользовательского средства ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-599">Create a custom logger</span></span>

<span data-ttu-id="1f24b-600">Чтобы добавить пользовательское средство ведения журнала, добавьте <xref:Microsoft.Extensions.Logging.ILoggerProvider> с <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="1f24b-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="1f24b-601">`ILoggerProvider` создает один или несколько экземпляров `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="1f24b-602">Экземпляры `ILogger` используются платформой для записи данных в журнал.</span><span class="sxs-lookup"><span data-stu-id="1f24b-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="1f24b-603">Пример конфигурации пользовательского средства ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-603">Sample custom logger configuration</span></span>

<span data-ttu-id="1f24b-604">Этот пример выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="1f24b-604">The sample:</span></span>

* <span data-ttu-id="1f24b-605">В этом простом примере задается цвет консоли журнала в соответствии с идентификатором события и уровнем ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="1f24b-606">Как правило, средства ведения журнала не изменяются по идентификатору события и не зависят от уровня ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="1f24b-607">В этом примере создаются записи консоли разного цвета в соответствии с уровнем ведения журнала и идентификатором события с использованием следующего типа конфигурации:</span><span class="sxs-lookup"><span data-stu-id="1f24b-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="1f24b-608">Приведенный выше код задает уровень по умолчанию `Warning` и цвет `Yellow`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="1f24b-609">Если для `EventId` задано значение 0, в журнал записываются все события.</span><span class="sxs-lookup"><span data-stu-id="1f24b-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="1f24b-610">Создание пользовательского средства ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-610">Create the custom logger</span></span>

<span data-ttu-id="1f24b-611">В качестве имени категории реализации `ILogger`, как правило, используется источник журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="1f24b-612">Например, тип, в котором создается средство ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="1f24b-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="1f24b-613">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="1f24b-613">The preceding code:</span></span>

* <span data-ttu-id="1f24b-614">создает экземпляр средства ведения журнала по имени категории;</span><span class="sxs-lookup"><span data-stu-id="1f24b-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="1f24b-615">проверяет `logLevel == _config.LogLevel` в `IsEnabled`, благодаря чему каждому `logLevel` соответствует уникальное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="1f24b-616">Как правило, средства ведения журнала также должны быть включены для всех более высоких уровней ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="1f24b-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="1f24b-617">Создание пользовательского LoggerProvider</span><span class="sxs-lookup"><span data-stu-id="1f24b-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="1f24b-618">`LoggerProvider` — это класс, который создает экземпляры средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="1f24b-619">Возможно, создавать экземпляры средства ведения журнала для каждой категории не требуется, однако это может быть полезно для некоторых таких средств, например NLog или log4net.</span><span class="sxs-lookup"><span data-stu-id="1f24b-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="1f24b-620">Таким образом, при необходимости вы можете выбрать разные целевые объекты вывода журнала для каждой категории:</span><span class="sxs-lookup"><span data-stu-id="1f24b-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="1f24b-621">В приведенном выше коде <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> создает один экземпляр `ColorConsoleLogger` для каждого имени категории и сохраняет его в [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2).</span><span class="sxs-lookup"><span data-stu-id="1f24b-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="1f24b-622">Использование и регистрация пользовательского средства ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="1f24b-623">Средство ведения журнала регистрируется в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="1f24b-624">В приведенном выше коде необходимо указать по крайней мере один метод расширения для `ILoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1f24b-625">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f24b-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="1f24b-626">Ошибки, связанные с ведением журналов, следует создавать в репозитории [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues).</span><span class="sxs-lookup"><span data-stu-id="1f24b-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f24b-627">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="1f24b-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1f24b-628">.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="1f24b-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1f24b-629">В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="1f24b-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1f24b-630">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f24b-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="1f24b-631">Добавление поставщиков</span><span class="sxs-lookup"><span data-stu-id="1f24b-631">Add providers</span></span>

<span data-ttu-id="1f24b-632">Поставщик ведения журналов отображает или сохраняет журналы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="1f24b-633">Например, поставщик Console отображает журналы на консоли, а поставщик Azure Application Insights может сохранить их в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1f24b-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="1f24b-634">Журналы могут отправляться в несколько назначений после добавления нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="1f24b-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="1f24b-635">Для добавления поставщика вызовите метод расширения `Add{provider name}` поставщика в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f24b-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="1f24b-636">В указанном выше коде необходимо указать ссылки на `Microsoft.Extensions.Logging` и `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="1f24b-637">Шаблон проекта по умолчанию вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журналов:</span><span class="sxs-lookup"><span data-stu-id="1f24b-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="1f24b-638">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-638">Console</span></span>
* <span data-ttu-id="1f24b-639">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-639">Debug</span></span>
* <span data-ttu-id="1f24b-640">EventSource (начиная с версии ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="1f24b-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="1f24b-641">Если вы используете `CreateDefaultBuilder`, поставщики по умолчанию можно заменить собственными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="1f24b-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="1f24b-642">Вызовите <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> и добавьте требуемые поставщики.</span><span class="sxs-lookup"><span data-stu-id="1f24b-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="1f24b-643">См. сведения о [встроенных](#built-in-logging-providers) и [сторонних](#third-party-logging-providers) поставщиках ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="1f24b-644">Создание журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-644">Create logs</span></span>

<span data-ttu-id="1f24b-645">Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1f24b-646">В веб-приложении или размещенной службе получите `ILogger` посредством внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="1f24b-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="1f24b-647">В консольных приложениях без размещения используйте `LoggerFactory` для создания `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1f24b-648">В приведенном ниже примере для ASP.NET Core создается средство ведения журнала с категорией `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="1f24b-649">*Категория* ведения журналов представляет строку, которая связана с каждым журналом.</span><span class="sxs-lookup"><span data-stu-id="1f24b-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="1f24b-650">Экземпляр `ILogger<T>`, предоставляемый внедрением зависимостей, создает журналы с полным именем типа `T` в качестве категории.</span><span class="sxs-lookup"><span data-stu-id="1f24b-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="1f24b-651">В приведенных ниже примерах ASP.NET Core и консольного приложения средство ведения журнала используется для создания журналов с уровнем `Information`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1f24b-652">*Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.</span><span class="sxs-lookup"><span data-stu-id="1f24b-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="1f24b-653">[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="1f24b-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="1f24b-654">Создание журналов в классе Startup</span><span class="sxs-lookup"><span data-stu-id="1f24b-654">Create logs in Startup</span></span>

<span data-ttu-id="1f24b-655">Для записи журналов в классе `Startup` включите параметр `ILogger` в сигнатуру конструктора:</span><span class="sxs-lookup"><span data-stu-id="1f24b-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="1f24b-656">Создание журналов в классе Program</span><span class="sxs-lookup"><span data-stu-id="1f24b-656">Create logs in the Program class</span></span>

<span data-ttu-id="1f24b-657">Для записи журналов в классе `Program` получите экземпляр `ILogger` путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="1f24b-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="1f24b-658">Ведение журнала во время создания узла не поддерживается напрямую.</span><span class="sxs-lookup"><span data-stu-id="1f24b-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1f24b-659">Однако можно использовать отдельное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-659">However, a separate logger can be used.</span></span> <span data-ttu-id="1f24b-660">В следующем примере для входа в `CreateWebHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/).</span><span class="sxs-lookup"><span data-stu-id="1f24b-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="1f24b-661">`AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1f24b-662">Асинхронные методы ведения журналов не выполняются</span><span class="sxs-lookup"><span data-stu-id="1f24b-662">No asynchronous logger methods</span></span>

<span data-ttu-id="1f24b-663">Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1f24b-664">Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище,</span><span class="sxs-lookup"><span data-stu-id="1f24b-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1f24b-665">а затем перемещайте их в медленное хранилище.</span><span class="sxs-lookup"><span data-stu-id="1f24b-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="1f24b-666">Например, если вы входите в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными.</span><span class="sxs-lookup"><span data-stu-id="1f24b-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1f24b-667">Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f24b-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1f24b-668">Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="1f24b-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="1f24b-669">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="1f24b-669">Configuration</span></span>

<span data-ttu-id="1f24b-670">Конфигурацию поставщика ведения журналов предоставляет как минимум один поставщик конфигураций:</span><span class="sxs-lookup"><span data-stu-id="1f24b-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="1f24b-671">Форматы файлов (INI, JSON и XML).</span><span class="sxs-lookup"><span data-stu-id="1f24b-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="1f24b-672">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-672">Command-line arguments.</span></span>
* <span data-ttu-id="1f24b-673">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="1f24b-673">Environment variables.</span></span>
* <span data-ttu-id="1f24b-674">Объекты .NET в памяти.</span><span class="sxs-lookup"><span data-stu-id="1f24b-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="1f24b-675">Незашифрованное хранилище [Secret Manager](xref:security/app-secrets) (Диспетчер секретов).</span><span class="sxs-lookup"><span data-stu-id="1f24b-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="1f24b-676">Зашифрованное пользовательское хранилище, например [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1f24b-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="1f24b-677">Пользовательские поставщики (установленные или созданные).</span><span class="sxs-lookup"><span data-stu-id="1f24b-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="1f24b-678">Например, конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файле параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="1f24b-679">В следующем примере показано содержимое типичного файла *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="1f24b-680">Свойство `Logging` может иметь свойство `LogLevel` и свойства поставщика журналов (здесь — Console).</span><span class="sxs-lookup"><span data-stu-id="1f24b-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="1f24b-681">Свойство `LogLevel` в разделе `Logging` указывает минимальный [уровень](#log-level) журнала для выбранных категорий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1f24b-682">В этом примере категории `System` и `Microsoft` записываются на уровне `Information`, а остальные — на уровне `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="1f24b-683">Другие свойства в разделе `Logging` определяют поставщиков ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="1f24b-684">Пример приведен для поставщика Console.</span><span class="sxs-lookup"><span data-stu-id="1f24b-684">The example is for the Console provider.</span></span> <span data-ttu-id="1f24b-685">Если поставщик поддерживает [области журналов](#log-scopes), `IncludeScopes` определяет, включены ли они.</span><span class="sxs-lookup"><span data-stu-id="1f24b-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1f24b-686">Свойство поставщика (например, `Console` в этом примере) также определять свойство `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="1f24b-687">`LogLevel` под поставщиком указывает уровни журнала для этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="1f24b-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="1f24b-688">Если уровни указаны в `Logging.{providername}.LogLevel`, они не переопределяют ничего из того, что задано в `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="1f24b-689">Например, рассмотрим следующий код JSON:</span><span class="sxs-lookup"><span data-stu-id="1f24b-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="1f24b-690">В приведенном выше коде JSON параметры поставщика `Console` переопределяют предшествующий уровень ведения журнала (по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="1f24b-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="1f24b-691">API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1f24b-692">Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1f24b-693">Например, [Поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider), который добавляется `CreateDefaultBuilder` для чтения файлов параметров, по умолчанию перезагружает конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="1f24b-694">Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="1f24b-695">Сведения о реализации поставщиков конфигураций см. в статье <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="1f24b-696">Пример выходных данных ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-696">Sample logging output</span></span>

<span data-ttu-id="1f24b-697">В примере кода из предыдущего раздела журналы появляются в консоли после запуска приложения из командной строки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="1f24b-698">Ниже приведен пример выходных данных консоли:</span><span class="sxs-lookup"><span data-stu-id="1f24b-698">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="1f24b-699">Предыдущие журналы созданы путем отправки HTTP-запроса Get к примеру приложения по адресу `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="1f24b-700">Ниже приведен пример этих журналов в том виде, в каком они отображаются в окне отладки при запуске примера приложения в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f24b-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="1f24b-701">Журналы, которые созданы путем вызовов `ILogger`, как показано в предыдущем разделе, начинаются с TodoApi.</span><span class="sxs-lookup"><span data-stu-id="1f24b-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="1f24b-702">Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1f24b-703">В ASP.NET Core и коде приложения используется один и тот же API ведения журналов и одни и те же поставщики.</span><span class="sxs-lookup"><span data-stu-id="1f24b-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="1f24b-704">В оставшейся части этой статьи рассматриваются некоторые сведения и параметры для ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="1f24b-705">Пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="1f24b-705">NuGet packages</span></span>

<span data-ttu-id="1f24b-706">Интерфейсы `ILogger` и `ILoggerFactory` находятся в [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), а их реализации по умолчанию — в [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="1f24b-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="1f24b-707">Категория журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-707">Log category</span></span>

<span data-ttu-id="1f24b-708">При создании объекта `ILogger` для него указывается *категория*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="1f24b-709">Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1f24b-710">Категория может быть любой строкой, обычно используется имя класса, например TodoApi.Controllers.TodoController.</span><span class="sxs-lookup"><span data-stu-id="1f24b-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="1f24b-711">Используйте `ILogger<T>` для получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:</span><span class="sxs-lookup"><span data-stu-id="1f24b-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="1f24b-712">Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="1f24b-713">Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="1f24b-714">Уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-714">Log level</span></span>

<span data-ttu-id="1f24b-715">Каждый журнал задает значение <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="1f24b-716">Уровень ведения журналов означает степень серьезности или важности.</span><span class="sxs-lookup"><span data-stu-id="1f24b-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="1f24b-717">Например, можно создать журнал `Information` при нормальном завершении метода и журнал `Warning`, если метод возвращает код состояния *404 — не найдено*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="1f24b-718">Следующий код создает журналы `Information`и `Warning`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1f24b-719">В приведенном выше коде параметры `MyLogEvents.GetItem` и `MyLogEvents.GetItemNotFound` являются [идентификатором события журнала](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="1f24b-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="1f24b-720">Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1f24b-721">Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#lmt) в этой статье.</span><span class="sxs-lookup"><span data-stu-id="1f24b-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="1f24b-722">Методы журналов, которые содержат уровень в имени метода (например, `LogInformation` и `LogWarning`), являются [методами расширения для ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="1f24b-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="1f24b-723">Эти методы вызывают метод `Log`, принимающий параметр `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="1f24b-724">Метод `Log`, в отличие от этих методов расширения, можно вызывать напрямую, однако его синтаксис довольно сложен.</span><span class="sxs-lookup"><span data-stu-id="1f24b-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="1f24b-725">См. сведения о <xref:Microsoft.Extensions.Logging.ILogger> и [исходном коде расширений средства ведения журналов](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="1f24b-726">В ASP.NET Core определяются следующие уровни ведения журналов, упорядоченные по возрастанию уровней серьезности.</span><span class="sxs-lookup"><span data-stu-id="1f24b-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="1f24b-727">Трассировка = 0</span><span class="sxs-lookup"><span data-stu-id="1f24b-727">Trace = 0</span></span>

  <span data-ttu-id="1f24b-728">Для получения сведений, которые полезны только при отладке.</span><span class="sxs-lookup"><span data-stu-id="1f24b-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="1f24b-729">Эти сообщения могут содержать конфиденциальные данные приложения, поэтому их не следует включать в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1f24b-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="1f24b-730">*По умолчанию отключено.*</span><span class="sxs-lookup"><span data-stu-id="1f24b-730">*Disabled by default.*</span></span>

* <span data-ttu-id="1f24b-731">Отладка = 1</span><span class="sxs-lookup"><span data-stu-id="1f24b-731">Debug = 1</span></span>

  <span data-ttu-id="1f24b-732">Для получения сведений, которые полезны при разработке и отладке.</span><span class="sxs-lookup"><span data-stu-id="1f24b-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="1f24b-733">Пример. `Entering method Configure with flag set to true.` Включайте уровни ведения журналов `Debug` в рабочей среде только при устранении неполадок, так как такие журналы занимают много места.</span><span class="sxs-lookup"><span data-stu-id="1f24b-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="1f24b-734">Информация = 2</span><span class="sxs-lookup"><span data-stu-id="1f24b-734">Information = 2</span></span>

  <span data-ttu-id="1f24b-735">Для отслеживания общего потока работы приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="1f24b-736">Эти журналы обычно полезны в долгосрочной перспективе.</span><span class="sxs-lookup"><span data-stu-id="1f24b-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="1f24b-737">Пример: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="1f24b-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="1f24b-738">Предупреждение = 3</span><span class="sxs-lookup"><span data-stu-id="1f24b-738">Warning = 3</span></span>

  <span data-ttu-id="1f24b-739">Для нестандартных или непредвиденных событий, возникающих в потоке работы приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="1f24b-740">Это могут быть ошибки или другие условия, которые не приводят к остановке приложения, но которые нужно изучить.</span><span class="sxs-lookup"><span data-stu-id="1f24b-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="1f24b-741">Обработанные исключения являются распространенным условием использования уровня ведения журнала `Warning`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="1f24b-742">Пример: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="1f24b-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="1f24b-743">Ошибка = 4</span><span class="sxs-lookup"><span data-stu-id="1f24b-743">Error = 4</span></span>

  <span data-ttu-id="1f24b-744">Для ошибок и исключений, которые не могут быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="1f24b-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1f24b-745">Эти сообщения указывают на сбой текущего действия или операции (например, текущего HTTP-запроса), а не на ошибку уровня приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="1f24b-746">Пример сообщения журнала: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="1f24b-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="1f24b-747">Критический = 5</span><span class="sxs-lookup"><span data-stu-id="1f24b-747">Critical = 5</span></span>

  <span data-ttu-id="1f24b-748">Для сбоев, которые требуют немедленного внимания.</span><span class="sxs-lookup"><span data-stu-id="1f24b-748">For failures that require immediate attention.</span></span> <span data-ttu-id="1f24b-749">Примеры: потеря данных, нехватка места на диске.</span><span class="sxs-lookup"><span data-stu-id="1f24b-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="1f24b-750">Уровень ведения журналов можно использовать для управления объемом выходных данных, записываемых на определенный носитель или в окно отображения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="1f24b-751">Пример:</span><span class="sxs-lookup"><span data-stu-id="1f24b-751">For example:</span></span>

* <span data-ttu-id="1f24b-752">В рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="1f24b-752">In production:</span></span>
  * <span data-ttu-id="1f24b-753">При ведении журнала на уровнях с `Trace` по `Information` создается большой объем подробных сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1f24b-754">Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях с `Trace` по `Information` в хранилище данных с низкими затратами и большим объемом.</span><span class="sxs-lookup"><span data-stu-id="1f24b-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="1f24b-755">Ведение журнала на уровнях с `Warning` по `Critical` обычно приводит к записи меньшего количества сообщений меньшего размера.</span><span class="sxs-lookup"><span data-stu-id="1f24b-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="1f24b-756">Следовательно, затраты и лимиты хранилища не становятся проблемой, что приводит к большей гибкости при выборе хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="1f24b-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="1f24b-757">Во время разработки:</span><span class="sxs-lookup"><span data-stu-id="1f24b-757">During development:</span></span>
  * <span data-ttu-id="1f24b-758">Записывайте сообщения уровней с `Warning` по `Critical` на консоль.</span><span class="sxs-lookup"><span data-stu-id="1f24b-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="1f24b-759">Добавляйте сообщения уровней с `Trace` по `Information` при устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="1f24b-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="1f24b-760">В разделе [Фильтрация журналов](#log-filtering) далее в этой статье приводятся сведения о том, как контролировать уровни журнала, обрабатываемые поставщиком.</span><span class="sxs-lookup"><span data-stu-id="1f24b-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="1f24b-761">ASP.NET Core создает журналы для событий платформы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1f24b-762">В примерах журнала ранее в этой статье не указывались журналы с уровнем ниже `Information`, поэтому журналы уровня `Debug` или `Trace` не создавались.</span><span class="sxs-lookup"><span data-stu-id="1f24b-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="1f24b-763">Ниже приведен пример журналов консоли, которые созданы при запуске примера приложения, настроенного на отображение журналов `Debug`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="1f24b-764">Идентификатор события журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-764">Log event ID</span></span>

<span data-ttu-id="1f24b-765">Каждый журнал может указывать *идентификатор события*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1f24b-766">В примере приложения для этого используется локально определенный класс `LoggingEvents`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="1f24b-767">Идентификатор события связывает набор событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-767">An event ID associates a set of events.</span></span> <span data-ttu-id="1f24b-768">Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.</span><span class="sxs-lookup"><span data-stu-id="1f24b-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1f24b-769">Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить.</span><span class="sxs-lookup"><span data-stu-id="1f24b-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1f24b-770">Поставщик Debug не отображает идентификаторы событий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1f24b-771">Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:</span><span class="sxs-lookup"><span data-stu-id="1f24b-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="1f24b-772">Шаблон сообщения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-772">Log message template</span></span>

<span data-ttu-id="1f24b-773">Каждый журнал указывает шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-773">Each log specifies a message template.</span></span> <span data-ttu-id="1f24b-774">Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1f24b-775">Используйте для заполнителей имена, а не числа.</span><span class="sxs-lookup"><span data-stu-id="1f24b-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1f24b-776">Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами.</span><span class="sxs-lookup"><span data-stu-id="1f24b-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1f24b-777">В приведенном ниже коде обратите внимание на то, что имена параметров идут не по порядку в шаблоне сообщения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1f24b-778">Этот код создает сообщение журнала со значениями параметров в определенном порядке:</span><span class="sxs-lookup"><span data-stu-id="1f24b-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="1f24b-779">Платформа ведения журналов поддерживает такое поведение, чтобы поставщики ведения журнала могли реализовывать [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1f24b-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="1f24b-780">Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1f24b-781">Эта информация позволяет поставщикам ведения журналов хранить значения параметров как поля.</span><span class="sxs-lookup"><span data-stu-id="1f24b-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1f24b-782">Предположим, например, что вызовы метода средства ведения журналов выглядят так:</span><span class="sxs-lookup"><span data-stu-id="1f24b-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1f24b-783">Если вы отправляете журналы в Хранилище таблиц Azure, каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`, что упрощает выполнение запросов к данным журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="1f24b-784">Запрос может находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="1f24b-785">Ведение журнала исключений</span><span class="sxs-lookup"><span data-stu-id="1f24b-785">Logging exceptions</span></span>

<span data-ttu-id="1f24b-786">Методы средства ведения журнала имеют перегрузки, которые позволяют передавать исключение, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1f24b-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="1f24b-787">Разные поставщики обрабатывают сведения об исключениях по-разному.</span><span class="sxs-lookup"><span data-stu-id="1f24b-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="1f24b-788">Ниже приведен пример выходных данных поставщика Debug из приведенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="1f24b-789">Фильтрация журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-789">Log filtering</span></span>

<span data-ttu-id="1f24b-790">Можно указать минимальный уровень ведения журнала для определенного поставщика и категории или для всех поставщиков или всех категорий.</span><span class="sxs-lookup"><span data-stu-id="1f24b-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="1f24b-791">Все журналы с уровнем ниже минимального не передаются этому поставщику, поэтому они не отображаются и не сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1f24b-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="1f24b-792">Чтобы проигнорировать все журналы, укажите `LogLevel.None` в качестве минимального уровня ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="1f24b-793">`LogLevel.None` равно целочисленному значению 6, то есть больше, чем `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="1f24b-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="1f24b-794">Создание правил фильтрации в конфигурации</span><span class="sxs-lookup"><span data-stu-id="1f24b-794">Create filter rules in configuration</span></span>

<span data-ttu-id="1f24b-795">Код шаблона проектов вызывает `CreateDefaultBuilder` для настройки ведения журналов для поставщиков Console, Debug и EventSource (ASP.NET Core 2.2 или более поздняя версия).</span><span class="sxs-lookup"><span data-stu-id="1f24b-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="1f24b-796">Метод `CreateDefaultBuilder` настраивает ведение журнала для просмотра конфигурации в разделе `Logging`, как было описано [ранее в этой статье](#configuration).</span><span class="sxs-lookup"><span data-stu-id="1f24b-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="1f24b-797">Данные конфигурации указывают минимальные уровни ведения журнала для каждого поставщика и категории, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1f24b-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="1f24b-798">Этот JSON создает шесть правил фильтрации: одно для поставщика Debug, четыре для поставщика Console и одно для всех поставщиков.</span><span class="sxs-lookup"><span data-stu-id="1f24b-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="1f24b-799">При создании объекта `ILogger` для каждого поставщика выбирается только одно из этих правил.</span><span class="sxs-lookup"><span data-stu-id="1f24b-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="1f24b-800">Правила фильтрации в коде</span><span class="sxs-lookup"><span data-stu-id="1f24b-800">Filter rules in code</span></span>

<span data-ttu-id="1f24b-801">В следующем примере показано, как зарегистрировать в коде правила фильтрации:</span><span class="sxs-lookup"><span data-stu-id="1f24b-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="1f24b-802">Второй `AddFilter` указывает поставщика, Debug, используя имя его типа.</span><span class="sxs-lookup"><span data-stu-id="1f24b-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="1f24b-803">Первый `AddFilter` применяется ко всем поставщикам, так как он не определяет тип поставщика.</span><span class="sxs-lookup"><span data-stu-id="1f24b-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1f24b-804">Применение правил фильтрации</span><span class="sxs-lookup"><span data-stu-id="1f24b-804">How filtering rules are applied</span></span>

<span data-ttu-id="1f24b-805">Данные конфигурации и код `AddFilter`, приведенный в предыдущих примерах, создают правила, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="1f24b-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="1f24b-806">Первые шесть взяты из примера конфигурации, а последние два — из примера кода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="1f24b-807">Число</span><span class="sxs-lookup"><span data-stu-id="1f24b-807">Number</span></span> | <span data-ttu-id="1f24b-808">Поставщик</span><span class="sxs-lookup"><span data-stu-id="1f24b-808">Provider</span></span>      | <span data-ttu-id="1f24b-809">Категории, которые начинаются с...</span><span class="sxs-lookup"><span data-stu-id="1f24b-809">Categories that begin with ...</span></span>          | <span data-ttu-id="1f24b-810">Минимальный уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1f24b-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="1f24b-811">1</span><span class="sxs-lookup"><span data-stu-id="1f24b-811">1</span></span>      | <span data-ttu-id="1f24b-812">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-812">Debug</span></span>         | <span data-ttu-id="1f24b-813">Все категории</span><span class="sxs-lookup"><span data-stu-id="1f24b-813">All categories</span></span>                          | <span data-ttu-id="1f24b-814">Сведения</span><span class="sxs-lookup"><span data-stu-id="1f24b-814">Information</span></span>       |
| <span data-ttu-id="1f24b-815">2</span><span class="sxs-lookup"><span data-stu-id="1f24b-815">2</span></span>      | <span data-ttu-id="1f24b-816">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-816">Console</span></span>       | <span data-ttu-id="1f24b-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="1f24b-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="1f24b-818">Предупреждение</span><span class="sxs-lookup"><span data-stu-id="1f24b-818">Warning</span></span>           |
| <span data-ttu-id="1f24b-819">3</span><span class="sxs-lookup"><span data-stu-id="1f24b-819">3</span></span>      | <span data-ttu-id="1f24b-820">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-820">Console</span></span>       | <span data-ttu-id="1f24b-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="1f24b-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="1f24b-822">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-822">Debug</span></span>             |
| <span data-ttu-id="1f24b-823">4</span><span class="sxs-lookup"><span data-stu-id="1f24b-823">4</span></span>      | <span data-ttu-id="1f24b-824">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-824">Console</span></span>       | <span data-ttu-id="1f24b-825">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="1f24b-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="1f24b-826">Ошибка</span><span class="sxs-lookup"><span data-stu-id="1f24b-826">Error</span></span>             |
| <span data-ttu-id="1f24b-827">5</span><span class="sxs-lookup"><span data-stu-id="1f24b-827">5</span></span>      | <span data-ttu-id="1f24b-828">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-828">Console</span></span>       | <span data-ttu-id="1f24b-829">Все категории</span><span class="sxs-lookup"><span data-stu-id="1f24b-829">All categories</span></span>                          | <span data-ttu-id="1f24b-830">Сведения</span><span class="sxs-lookup"><span data-stu-id="1f24b-830">Information</span></span>       |
| <span data-ttu-id="1f24b-831">6</span><span class="sxs-lookup"><span data-stu-id="1f24b-831">6</span></span>      | <span data-ttu-id="1f24b-832">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="1f24b-832">All providers</span></span> | <span data-ttu-id="1f24b-833">Все категории</span><span class="sxs-lookup"><span data-stu-id="1f24b-833">All categories</span></span>                          | <span data-ttu-id="1f24b-834">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-834">Debug</span></span>             |
| <span data-ttu-id="1f24b-835">7</span><span class="sxs-lookup"><span data-stu-id="1f24b-835">7</span></span>      | <span data-ttu-id="1f24b-836">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="1f24b-836">All providers</span></span> | <span data-ttu-id="1f24b-837">Система</span><span class="sxs-lookup"><span data-stu-id="1f24b-837">System</span></span>                                  | <span data-ttu-id="1f24b-838">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-838">Debug</span></span>             |
| <span data-ttu-id="1f24b-839">8</span><span class="sxs-lookup"><span data-stu-id="1f24b-839">8</span></span>      | <span data-ttu-id="1f24b-840">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-840">Debug</span></span>         | <span data-ttu-id="1f24b-841">Майкрософт</span><span class="sxs-lookup"><span data-stu-id="1f24b-841">Microsoft</span></span>                               | <span data-ttu-id="1f24b-842">Трассировка</span><span class="sxs-lookup"><span data-stu-id="1f24b-842">Trace</span></span>             |

<span data-ttu-id="1f24b-843">При создании объекта `ILogger` объект `ILoggerFactory` выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1f24b-844">Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил.</span><span class="sxs-lookup"><span data-stu-id="1f24b-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1f24b-845">Самое подходящее правило для каждой пары поставщика и категории выбирается из списка доступных правил.</span><span class="sxs-lookup"><span data-stu-id="1f24b-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1f24b-846">При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:</span><span class="sxs-lookup"><span data-stu-id="1f24b-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1f24b-847">Выберите все правила, которые соответствуют поставщику или его псевдониму.</span><span class="sxs-lookup"><span data-stu-id="1f24b-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1f24b-848">Если ничего не найдено, выберите все правила с пустым поставщиком.</span><span class="sxs-lookup"><span data-stu-id="1f24b-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1f24b-849">В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории.</span><span class="sxs-lookup"><span data-stu-id="1f24b-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1f24b-850">Если ничего не найдено, выберите все правила, которые не указывают категорию.</span><span class="sxs-lookup"><span data-stu-id="1f24b-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1f24b-851">Если выбрано несколько правил, примите **последнее**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1f24b-852">Если правила не выбраны, используйте `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="1f24b-853">Предположим, у вас есть указанный выше список правил и вы создаете объект `ILogger` для категории Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine:</span><span class="sxs-lookup"><span data-stu-id="1f24b-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="1f24b-854">К поставщику Debug применяются правила 1, 6 и 8.</span><span class="sxs-lookup"><span data-stu-id="1f24b-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="1f24b-855">Правило 8 является наиболее подходящим, поэтому выбрано оно.</span><span class="sxs-lookup"><span data-stu-id="1f24b-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="1f24b-856">К поставщику отладки применяются правила 3, 4, 5 и 6.</span><span class="sxs-lookup"><span data-stu-id="1f24b-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="1f24b-857">Правило 3 является наиболее подходящим.</span><span class="sxs-lookup"><span data-stu-id="1f24b-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="1f24b-858">Полученный в результате экземпляр `ILogger` отправляет журналы уровня `Trace` и выше в поставщик Debug.</span><span class="sxs-lookup"><span data-stu-id="1f24b-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="1f24b-859">Журналы уровня `Debug` и выше отправляются в поставщик Console.</span><span class="sxs-lookup"><span data-stu-id="1f24b-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="1f24b-860">Псевдонимы поставщиков</span><span class="sxs-lookup"><span data-stu-id="1f24b-860">Provider aliases</span></span>

<span data-ttu-id="1f24b-861">Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа.</span><span class="sxs-lookup"><span data-stu-id="1f24b-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="1f24b-862">Для встроенных поставщиков используйте следующие псевдонимы:</span><span class="sxs-lookup"><span data-stu-id="1f24b-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="1f24b-863">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-863">Console</span></span>
* <span data-ttu-id="1f24b-864">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-864">Debug</span></span>
* <span data-ttu-id="1f24b-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-865">EventSource</span></span>
* <span data-ttu-id="1f24b-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-866">EventLog</span></span>
* <span data-ttu-id="1f24b-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-867">TraceSource</span></span>
* <span data-ttu-id="1f24b-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1f24b-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="1f24b-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1f24b-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="1f24b-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1f24b-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="1f24b-871">Минимальный уровень по умолчанию</span><span class="sxs-lookup"><span data-stu-id="1f24b-871">Default minimum level</span></span>

<span data-ttu-id="1f24b-872">Существует параметр минимального уровня, который применяется, только если к определенному поставщику или категории не подходят правила из конфигурации или кода.</span><span class="sxs-lookup"><span data-stu-id="1f24b-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="1f24b-873">В следующем примере показано, как задать минимальный уровень:</span><span class="sxs-lookup"><span data-stu-id="1f24b-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="1f24b-874">Если минимальный уровень не задан явным образом, используется значение по умолчанию — `Information`, означающее, что журналы `Trace` и `Debug` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="1f24b-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="1f24b-875">Функции фильтрации</span><span class="sxs-lookup"><span data-stu-id="1f24b-875">Filter functions</span></span>

<span data-ttu-id="1f24b-876">Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила.</span><span class="sxs-lookup"><span data-stu-id="1f24b-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="1f24b-877">Код в функции имеет доступ к типу поставщика, категории и уровню ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="1f24b-878">Пример:</span><span class="sxs-lookup"><span data-stu-id="1f24b-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="1f24b-879">Системные категории и уровни</span><span class="sxs-lookup"><span data-stu-id="1f24b-879">System categories and levels</span></span>

<span data-ttu-id="1f24b-880">Ниже приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:</span><span class="sxs-lookup"><span data-stu-id="1f24b-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="1f24b-881">Категория</span><span class="sxs-lookup"><span data-stu-id="1f24b-881">Category</span></span>                            | <span data-ttu-id="1f24b-882">Примечания</span><span class="sxs-lookup"><span data-stu-id="1f24b-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1f24b-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1f24b-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1f24b-884">Общая диагностика ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1f24b-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1f24b-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1f24b-886">Распознанные, найденные и использованные ключи.</span><span class="sxs-lookup"><span data-stu-id="1f24b-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1f24b-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1f24b-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1f24b-888">Разрешенные узлы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-888">Hosts allowed.</span></span> |
| <span data-ttu-id="1f24b-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1f24b-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1f24b-890">Время начала и длительность выполнения HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1f24b-891">Определение загруженных начальных сборок размещения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1f24b-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1f24b-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1f24b-893">Диагностика MVC и Razor.</span><span class="sxs-lookup"><span data-stu-id="1f24b-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1f24b-894">Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия.</span><span class="sxs-lookup"><span data-stu-id="1f24b-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1f24b-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1f24b-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1f24b-896">Перенаправление соответствующих сведений.</span><span class="sxs-lookup"><span data-stu-id="1f24b-896">Route matching information.</span></span> |
| <span data-ttu-id="1f24b-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1f24b-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1f24b-898">Запуск, остановка и сохранение ответов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1f24b-899">Сведения о сертификате HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1f24b-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1f24b-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1f24b-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1f24b-901">Обработанные файлы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-901">Files served.</span></span> |
| <span data-ttu-id="1f24b-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1f24b-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1f24b-903">Общая диагностика Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1f24b-904">Действия и конфигурация базы данных, обнаружение изменений, переносы.</span><span class="sxs-lookup"><span data-stu-id="1f24b-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="1f24b-905">Области журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-905">Log scopes</span></span>

 <span data-ttu-id="1f24b-906">*Область* может группировать набор логических операций.</span><span class="sxs-lookup"><span data-stu-id="1f24b-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1f24b-907">Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора.</span><span class="sxs-lookup"><span data-stu-id="1f24b-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1f24b-908">Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.</span><span class="sxs-lookup"><span data-stu-id="1f24b-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1f24b-909">Область — это тип `IDisposable`, возвращаемый методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> и действующий до удаления.</span><span class="sxs-lookup"><span data-stu-id="1f24b-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="1f24b-910">Используйте область, заключив вызовы средства ведения журналов в блок `using`:</span><span class="sxs-lookup"><span data-stu-id="1f24b-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="1f24b-911">Следующий код предоставляет области для поставщика Console:</span><span class="sxs-lookup"><span data-stu-id="1f24b-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="1f24b-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f24b-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="1f24b-913">Для включения ведения журнала на уровне области требуется параметр `IncludeScopes` средства ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="1f24b-914">Сведения о настройках см. в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="1f24b-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="1f24b-915">Каждое сообщение журнала содержит ограниченную информацию:</span><span class="sxs-lookup"><span data-stu-id="1f24b-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="1f24b-916">Встроенные поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-916">Built-in logging providers</span></span>

<span data-ttu-id="1f24b-917">В состав ASP.NET Core входят следующие поставщики:</span><span class="sxs-lookup"><span data-stu-id="1f24b-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="1f24b-918">Консоль</span><span class="sxs-lookup"><span data-stu-id="1f24b-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="1f24b-919">Отладка</span><span class="sxs-lookup"><span data-stu-id="1f24b-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1f24b-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="1f24b-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="1f24b-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="1f24b-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1f24b-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1f24b-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1f24b-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1f24b-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1f24b-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="1f24b-926">Сведения о stdout и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="1f24b-927">Поставщик Console</span><span class="sxs-lookup"><span data-stu-id="1f24b-927">Console provider</span></span>

<span data-ttu-id="1f24b-928">Пакет поставщика [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) отправляет выходные данные журнала на консоль.</span><span class="sxs-lookup"><span data-stu-id="1f24b-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="1f24b-929">Чтобы просмотреть выходные данные ведения журналов в консоли, откройте командную строку, перейдите в папку проекта и запустите приведенную ниже команду:</span><span class="sxs-lookup"><span data-stu-id="1f24b-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="1f24b-930">Поставщик Debug</span><span class="sxs-lookup"><span data-stu-id="1f24b-930">Debug provider</span></span>

<span data-ttu-id="1f24b-931">Пакет поставщика [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) записывает выходные данные журналов с помощью класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (вызовов метода `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="1f24b-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="1f24b-932">В Linux этот поставщик записывает журналы в каталог */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="1f24b-933">Поставщик источника событий</span><span class="sxs-lookup"><span data-stu-id="1f24b-933">Event Source provider</span></span>

<span data-ttu-id="1f24b-934">Пакет поставщика [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) записывает данные в источник событий на кросс-платформенный сервер с именем `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1f24b-935">В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="1f24b-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="1f24b-936">Поставщик источника событий добавляется автоматически при вызове `CreateDefaultBuilder` для сборки узла.</span><span class="sxs-lookup"><span data-stu-id="1f24b-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="1f24b-937">Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="1f24b-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1f24b-938">Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1f24b-939">Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1f24b-940">(Не пропустите звездочку в начале строки.)</span><span class="sxs-lookup"><span data-stu-id="1f24b-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Дополнительные поставщики Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="1f24b-942">Поставщик Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="1f24b-942">Windows EventLog provider</span></span>

<span data-ttu-id="1f24b-943">Пакет поставщика [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) отправляет выходные данные журнала в журнал событий Windows.</span><span class="sxs-lookup"><span data-stu-id="1f24b-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="1f24b-944">[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1f24b-945">Если `null` или не указан, используются следующие параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1f24b-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1f24b-946">`LogName`. "Application"</span><span class="sxs-lookup"><span data-stu-id="1f24b-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="1f24b-947">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="1f24b-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="1f24b-948">`MachineName`. Используется имя локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="1f24b-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="1f24b-949">События регистрируются для [уровня предупреждения и более высоких уровней](#log-level).</span><span class="sxs-lookup"><span data-stu-id="1f24b-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="1f24b-950">В следующем примере для журнала событий по умолчанию задается уровень <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="1f24b-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="1f24b-951">Поставщик TraceSource</span><span class="sxs-lookup"><span data-stu-id="1f24b-951">TraceSource provider</span></span>

<span data-ttu-id="1f24b-952">Пакет поставщика [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) использует библиотеки и поставщики <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="1f24b-953">[Перегрузки AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) позволяют передавать исходный параметр и прослушиватель трассировки.</span><span class="sxs-lookup"><span data-stu-id="1f24b-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="1f24b-954">Чтобы использовать этот поставщик, приложение должно выполняться в .NET Framework (а не в .NET Core).</span><span class="sxs-lookup"><span data-stu-id="1f24b-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="1f24b-955">Поставщик позволяет перенаправлять сообщения различным [прослушивателям](/dotnet/framework/debug-trace-profile/trace-listeners), например <xref:System.Diagnostics.TextWriterTraceListener>, который используется в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="1f24b-956">Поставщик службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-956">Azure App Service provider</span></span>

<span data-ttu-id="1f24b-957">Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="1f24b-958">Этот пакет не входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1f24b-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="1f24b-959">Если планируется использовать .NET Framework или будет указана ссылка на метапакет `Microsoft.AspNetCore.App`, добавьте пакет поставщика в проект.</span><span class="sxs-lookup"><span data-stu-id="1f24b-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="1f24b-960">Перегрузка <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> позволяет передавать <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="1f24b-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="1f24b-961">Объект параметров может переопределять параметры по умолчанию, например шаблон выходных данных ведения журналов, имя BLOB-объекта и максимально допустимый размер файла.</span><span class="sxs-lookup"><span data-stu-id="1f24b-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="1f24b-962">(*Шаблон выходных данных* — это шаблон сообщений, который применяется ко всем журналам наряду с тем, что предоставляется при вызове метода `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="1f24b-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="1f24b-963">При развертывании в приложение Службы приложений ваше приложение использует параметры в разделе [Журналы Службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) на странице **Служба приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1f24b-964">При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:</span><span class="sxs-lookup"><span data-stu-id="1f24b-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1f24b-965">**Ведение журнала приложения (файловая система)** ;</span><span class="sxs-lookup"><span data-stu-id="1f24b-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1f24b-966">**Ведение журнала приложения (BLOB-объект)** .</span><span class="sxs-lookup"><span data-stu-id="1f24b-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1f24b-967">По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1f24b-968">Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2.</span><span class="sxs-lookup"><span data-stu-id="1f24b-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1f24b-969">Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="1f24b-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1f24b-970">Поставщик работает только при выполнении проекта в среде Azure.</span><span class="sxs-lookup"><span data-stu-id="1f24b-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="1f24b-971">Он не функционирует при запуске проекта в локальной среде &mdash;(то есть не выполняет запись в локальные файлы или в локальное хранилище разработки для больших двоичных объектов).</span><span class="sxs-lookup"><span data-stu-id="1f24b-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1f24b-972">Потоковая передача журналов Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-972">Azure log streaming</span></span>

<span data-ttu-id="1f24b-973">Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="1f24b-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="1f24b-974">сервер приложений;</span><span class="sxs-lookup"><span data-stu-id="1f24b-974">The app server</span></span>
* <span data-ttu-id="1f24b-975">веб-сервер;</span><span class="sxs-lookup"><span data-stu-id="1f24b-975">The web server</span></span>
* <span data-ttu-id="1f24b-976">трассировка неудачно завершенных запросов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-976">Failed request tracing</span></span>

<span data-ttu-id="1f24b-977">Настройка потоковой передачи журналов Azure</span><span class="sxs-lookup"><span data-stu-id="1f24b-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1f24b-978">Со страницы портала приложения перейдите на страницу **Журналы Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="1f24b-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="1f24b-979">**Включите** параметр **Ведение журнала приложения (файловая система)** .</span><span class="sxs-lookup"><span data-stu-id="1f24b-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1f24b-980">Выберите **уровень** ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="1f24b-980">Choose the log **Level**.</span></span> <span data-ttu-id="1f24b-981">Этот параметр применяется только к потоковой передаче журналов Azure, а не к другим поставщикам ведения журнала в приложении.</span><span class="sxs-lookup"><span data-stu-id="1f24b-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="1f24b-982">Перейдите на страницу **Поток журналов**, чтобы просмотреть сообщения приложения.</span><span class="sxs-lookup"><span data-stu-id="1f24b-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="1f24b-983">Они записываются в журнал приложением через интерфейс `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="1f24b-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="1f24b-984">Ведение журнала трассировки Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1f24b-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="1f24b-985">Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1f24b-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="1f24b-986">Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии.</span><span class="sxs-lookup"><span data-stu-id="1f24b-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1f24b-987">Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.</span><span class="sxs-lookup"><span data-stu-id="1f24b-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1f24b-988">Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f24b-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1f24b-989">Если вы используете этот пакет, пакет поставщика устанавливать не нужно.</span><span class="sxs-lookup"><span data-stu-id="1f24b-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1f24b-990">Не используйте пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web), который предназначен для ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="1f24b-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="1f24b-991">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="1f24b-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1f24b-992">Общие сведения об Application Insights</span><span class="sxs-lookup"><span data-stu-id="1f24b-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1f24b-993">[Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1f24b-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.</span><span class="sxs-lookup"><span data-stu-id="1f24b-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1f24b-995">[Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="1f24b-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1f24b-996">[Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="1f24b-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1f24b-997">Сторонние поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="1f24b-997">Third-party logging providers</span></span>

<span data-ttu-id="1f24b-998">Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f24b-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1f24b-999">[ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1f24b-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1f24b-1001">[JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));</span><span class="sxs-lookup"><span data-stu-id="1f24b-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1f24b-1002">[KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1f24b-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1f24b-1003">[Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1f24b-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1f24b-1004">[Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-1005">[NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="1f24b-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1f24b-1006">[Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1f24b-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1f24b-1007">[Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).</span><span class="sxs-lookup"><span data-stu-id="1f24b-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1f24b-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1f24b-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1f24b-1009">Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1f24b-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1f24b-1010">Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:</span><span class="sxs-lookup"><span data-stu-id="1f24b-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1f24b-1011">Добавьте пакет NuGet в проект.</span><span class="sxs-lookup"><span data-stu-id="1f24b-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1f24b-1012">Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="1f24b-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1f24b-1013">Дополнительные сведения см. в документации по каждому поставщику.</span><span class="sxs-lookup"><span data-stu-id="1f24b-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1f24b-1014">Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="1f24b-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f24b-1015">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f24b-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
