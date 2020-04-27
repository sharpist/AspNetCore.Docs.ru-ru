---
title: Ведение журнала в .NET Core и ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать платформу ведения журналов, предоставляемую пакетом NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110919"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="e8b21-103">Ведение журнала в .NET Core и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8b21-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8b21-104">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="e8b21-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e8b21-105">.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="e8b21-106">В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="e8b21-107">Большинство примеров кода, приведенных в этой статье, взяты из приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="e8b21-108">Части этих фрагментов кода, относящиеся к ведению журнала, применимы ко всем приложениям .NET Core, использующим [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e8b21-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="e8b21-109">Пример использования универсального узла в приложении, не являющемся веб-консолью, см. в файле *Program.cs* [примера приложения "Фоновые задачи"](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="e8b21-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="e8b21-110">Код ведения журнала для приложений без универсального узла отличается тем, как [добавляются поставщики](#add-providers) и [создаются средства ведения журнала](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="e8b21-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="e8b21-111">Примеры кода, не связанные с размещением, приведены в соответствующих разделах статьи.</span><span class="sxs-lookup"><span data-stu-id="e8b21-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="e8b21-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8b21-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="e8b21-113">Добавление поставщиков</span><span class="sxs-lookup"><span data-stu-id="e8b21-113">Add providers</span></span>

<span data-ttu-id="e8b21-114">Поставщик ведения журналов отображает или сохраняет журналы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="e8b21-115">Например, поставщик Console отображает журналы на консоли, а поставщик Azure Application Insights может сохранить их в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="e8b21-116">Журналы могут отправляться в несколько назначений после добавления нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e8b21-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="e8b21-117">Чтобы добавить поставщик в приложение, использующее универсальный узел, вызовите метод расширения `Add{provider name}` поставщика в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="e8b21-118">В консольном приложении, не использующем узел, вызовите метод расширения `Add{provider name}` поставщика при создании `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="e8b21-119">`LoggerFactory` и `AddConsole` требуют оператор `using` для `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="e8b21-120">Шаблоны проектов ASP.NET Core по умолчанию вызывают метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журналов:</span><span class="sxs-lookup"><span data-stu-id="e8b21-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="e8b21-121">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="e8b21-122">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="e8b21-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="e8b21-124">[Журнал событий](#windows-eventlog-provider) (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="e8b21-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="e8b21-125">Поставщики по умолчанию можно заменить собственными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="e8b21-126">Вызовите <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> и добавьте требуемые поставщики.</span><span class="sxs-lookup"><span data-stu-id="e8b21-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="e8b21-127">См. сведения о [встроенных](#built-in-logging-providers) и [сторонних](#third-party-logging-providers) поставщиках ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="e8b21-128">Создание журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-128">Create logs</span></span>

<span data-ttu-id="e8b21-129">Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="e8b21-130">В веб-приложении или размещенной службе получите `ILogger` посредством внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="e8b21-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="e8b21-131">В консольных приложениях без размещения используйте `LoggerFactory` для создания `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="e8b21-132">В приведенном ниже примере для ASP.NET Core создается средство ведения журнала с категорией `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="e8b21-133">*Категория* ведения журналов представляет строку, которая связана с каждым журналом.</span><span class="sxs-lookup"><span data-stu-id="e8b21-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="e8b21-134">Экземпляр `ILogger<T>`, предоставляемый внедрением зависимостей, создает журналы с полным именем типа `T` в качестве категории.</span><span class="sxs-lookup"><span data-stu-id="e8b21-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="e8b21-135">В приведенном ниже примере консольного приложения без размещения создается средство ведения журнала с категорией `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="e8b21-136">В приведенных ниже примерах ASP.NET Core и консольного приложения средство ведения журнала используется для создания журналов с уровнем `Information`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="e8b21-137">*Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.</span><span class="sxs-lookup"><span data-stu-id="e8b21-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="e8b21-138">[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="e8b21-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="e8b21-139">Создание журналов в классе Program</span><span class="sxs-lookup"><span data-stu-id="e8b21-139">Create logs in the Program class</span></span>

<span data-ttu-id="e8b21-140">Для записи журналов в классе `Program` приложения ASP.NET Core получите экземпляр `ILogger` путем внедрения зависимостей после создания узла:</span><span class="sxs-lookup"><span data-stu-id="e8b21-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="e8b21-141">Ведение журнала во время создания узла не поддерживается напрямую.</span><span class="sxs-lookup"><span data-stu-id="e8b21-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="e8b21-142">Однако можно использовать отдельное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-142">However, a separate logger can be used.</span></span> <span data-ttu-id="e8b21-143">В следующем примере для входа в `CreateHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/).</span><span class="sxs-lookup"><span data-stu-id="e8b21-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="e8b21-144">`AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="e8b21-145">Создание журналов в классе Startup</span><span class="sxs-lookup"><span data-stu-id="e8b21-145">Create logs in the Startup class</span></span>

<span data-ttu-id="e8b21-146">Для записи журналов в методе `Startup.Configure` приложения ASP.NET Core включите параметр `ILogger` в сигнатуру метода:</span><span class="sxs-lookup"><span data-stu-id="e8b21-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="e8b21-147">Запись журналов до завершения настройки контейнера внедрения зависимостей в методе `Startup.ConfigureServices` не поддерживается:</span><span class="sxs-lookup"><span data-stu-id="e8b21-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="e8b21-148">Внедрение средства ведения журнала в конструктор `Startup` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="e8b21-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="e8b21-149">Внедрение средства ведения журнала в сигнатуру метода `Startup.ConfigureServices` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="e8b21-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="e8b21-150">Причина этого ограничения заключается в том, что ведение журнала зависит от внедрения зависимостей и от конфигурации, которая, в свою очередь, зависит от внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e8b21-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="e8b21-151">Контейнер внедрения зависимостей не настраивается, пока не завершится выполнение `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="e8b21-152">Внедрение средства ведения журнала в класс `Startup` посредством конструктора работает в более ранних версиях ASP.NET Core, так как для веб-узла создается отдельный контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e8b21-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="e8b21-153">Сведения о том, почему для универсального узла создается только один контейнер, см. в [объявлении о критическом изменении](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="e8b21-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="e8b21-154">Если необходимо настроить службу, которая зависит от `ILogger<T>`, это можно сделать с помощью внедрения конструктора или путем предоставления фабричного метода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="e8b21-155">Фабричный метод рекомендуется использовать, только если нет других вариантов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="e8b21-156">Например, предположим, что необходимо заполнить свойство службой из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="e8b21-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="e8b21-157">Выделенный выше код — это функция `Func`, которая выполняется, когда контейнеру внедрения зависимостей впервые требуется создать экземпляр `MyService`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="e8b21-158">Таким образом можно обращаться к любым зарегистрированным службам.</span><span class="sxs-lookup"><span data-stu-id="e8b21-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="e8b21-159">Создание журналов на платформе Blazor</span><span class="sxs-lookup"><span data-stu-id="e8b21-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="e8b21-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e8b21-160">Blazor WebAssembly</span></span>

<span data-ttu-id="e8b21-161">Настройте ведение журналов в приложениях Blazor WebAssembly с помощью свойства `WebAssemblyHostBuilder.Logging` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="e8b21-162">Свойство `Logging` имеет тип <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, поэтому все методы расширения, доступные в <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, также доступны в `Logging`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="e8b21-163">Журнал в компонентах Razor</span><span class="sxs-lookup"><span data-stu-id="e8b21-163">Log in Razor components</span></span>

<span data-ttu-id="e8b21-164">Средства ведения журнала учитывают конфигурацию запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="e8b21-165">Директива `using` для <xref:Microsoft.Extensions.Logging> обеспечивает поддержку завершения IntelliSense для таких интерфейсов API, как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="e8b21-166">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILogger> в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="e8b21-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="e8b21-167">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILoggerFactory> в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="e8b21-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="e8b21-168">Асинхронные методы ведения журналов не выполняются</span><span class="sxs-lookup"><span data-stu-id="e8b21-168">No asynchronous logger methods</span></span>

<span data-ttu-id="e8b21-169">Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="e8b21-170">Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище,</span><span class="sxs-lookup"><span data-stu-id="e8b21-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="e8b21-171">а затем перемещайте их в медленное хранилище.</span><span class="sxs-lookup"><span data-stu-id="e8b21-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="e8b21-172">Например, если вы входите в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными.</span><span class="sxs-lookup"><span data-stu-id="e8b21-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="e8b21-173">Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e8b21-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="e8b21-174">Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8b21-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="e8b21-175">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e8b21-175">Configuration</span></span>

<span data-ttu-id="e8b21-176">Конфигурацию поставщика ведения журналов предоставляет как минимум один поставщик конфигураций:</span><span class="sxs-lookup"><span data-stu-id="e8b21-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="e8b21-177">Форматы файлов (INI, JSON и XML).</span><span class="sxs-lookup"><span data-stu-id="e8b21-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="e8b21-178">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-178">Command-line arguments.</span></span>
* <span data-ttu-id="e8b21-179">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e8b21-179">Environment variables.</span></span>
* <span data-ttu-id="e8b21-180">Объекты .NET в памяти.</span><span class="sxs-lookup"><span data-stu-id="e8b21-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="e8b21-181">Незашифрованное хранилище [Secret Manager](xref:security/app-secrets) (Диспетчер секретов).</span><span class="sxs-lookup"><span data-stu-id="e8b21-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="e8b21-182">Зашифрованное пользовательское хранилище, например [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="e8b21-183">Пользовательские поставщики (установленные или созданные).</span><span class="sxs-lookup"><span data-stu-id="e8b21-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="e8b21-184">Например, конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файле параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="e8b21-185">В следующем примере показано содержимое типичного файла *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="e8b21-186">Свойство `Logging` может иметь свойство `LogLevel` и свойства поставщика журналов (здесь — Console).</span><span class="sxs-lookup"><span data-stu-id="e8b21-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="e8b21-187">Свойство `LogLevel` в разделе `Logging` указывает минимальный [уровень](#log-level) журнала для выбранных категорий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="e8b21-188">В этом примере категории `System` и `Microsoft` записываются на уровне `Information`, а остальные — на уровне `Debug`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="e8b21-189">Другие свойства в разделе `Logging` определяют поставщиков ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="e8b21-190">Пример приведен для поставщика Console.</span><span class="sxs-lookup"><span data-stu-id="e8b21-190">The example is for the Console provider.</span></span> <span data-ttu-id="e8b21-191">Если поставщик поддерживает [области журналов](#log-scopes), `IncludeScopes` определяет, включены ли они.</span><span class="sxs-lookup"><span data-stu-id="e8b21-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="e8b21-192">Свойство поставщика (например, `Console` в этом примере) также определять свойство `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="e8b21-193">`LogLevel` под поставщиком указывает уровни журнала для этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="e8b21-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="e8b21-194">Если уровни указаны в `Logging.{providername}.LogLevel`, они не переопределяют ничего из того, что задано в `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="e8b21-195">API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="e8b21-196">Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="e8b21-197">Например, [Поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider), который добавляется `CreateDefaultBuilder` для чтения файлов параметров, по умолчанию перезагружает конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="e8b21-198">Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="e8b21-199">Сведения о реализации поставщиков конфигураций см. в статье <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="e8b21-200">Пример выходных данных ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-200">Sample logging output</span></span>

<span data-ttu-id="e8b21-201">В примере кода из предыдущего раздела журналы появляются в консоли после запуска приложения из командной строки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="e8b21-202">Ниже приведен пример выходных данных консоли:</span><span class="sxs-lookup"><span data-stu-id="e8b21-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="e8b21-203">Предыдущие журналы созданы путем отправки HTTP-запроса Get к примеру приложения по адресу `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="e8b21-204">Ниже приведен пример этих журналов в том виде, в каком они отображаются в окне отладки при запуске примера приложения в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e8b21-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="e8b21-205">Журналы, которые созданы путем вызовов `ILogger`, как показано в предыдущем разделе, начинаются с TodoApiSample.</span><span class="sxs-lookup"><span data-stu-id="e8b21-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="e8b21-206">Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="e8b21-207">В ASP.NET Core и коде приложения используется один и тот же API ведения журналов и одни и те же поставщики.</span><span class="sxs-lookup"><span data-stu-id="e8b21-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="e8b21-208">В оставшейся части этой статьи рассматриваются некоторые сведения и параметры для ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="e8b21-209">Пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="e8b21-209">NuGet packages</span></span>

<span data-ttu-id="e8b21-210">Интерфейсы `ILogger` и `ILoggerFactory` находятся в [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), а их реализации по умолчанию — в [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="e8b21-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="e8b21-211">Категория журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-211">Log category</span></span>

<span data-ttu-id="e8b21-212">При создании объекта `ILogger` для него указывается *категория*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="e8b21-213">Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="e8b21-214">Категория может быть любой строкой, обычно используется имя класса, например TodoApi.Controllers.TodoController.</span><span class="sxs-lookup"><span data-stu-id="e8b21-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="e8b21-215">Используйте `ILogger<T>` для получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:</span><span class="sxs-lookup"><span data-stu-id="e8b21-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="e8b21-216">Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="e8b21-217">Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="e8b21-218">Уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-218">Log level</span></span>

<span data-ttu-id="e8b21-219">Каждый журнал задает значение <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="e8b21-220">Уровень ведения журналов означает степень серьезности или важности.</span><span class="sxs-lookup"><span data-stu-id="e8b21-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="e8b21-221">Например, можно создать журнал `Information` при нормальном завершении метода и журнал `Warning`, если метод возвращает код состояния *404 — не найдено*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="e8b21-222">Следующий код создает журналы `Information`и `Warning`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="e8b21-223">В коде выше первый параметр — это [идентификатор события журнала](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="e8b21-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="e8b21-224">Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="e8b21-225">Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#log-message-template) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="e8b21-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="e8b21-226">Методы журналов, которые содержат уровень в имени метода (например, `LogInformation` и `LogWarning`), являются [методами расширения для ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="e8b21-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="e8b21-227">Эти методы вызывают метод `Log`, принимающий параметр `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="e8b21-228">Метод `Log`, в отличие от этих методов расширения, можно вызывать напрямую, однако его синтаксис довольно сложен.</span><span class="sxs-lookup"><span data-stu-id="e8b21-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="e8b21-229">См. сведения о <xref:Microsoft.Extensions.Logging.ILogger> и [исходном коде расширений средства ведения журналов](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e8b21-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="e8b21-230">В ASP.NET Core определяются следующие уровни ведения журналов, упорядоченные по возрастанию уровней серьезности.</span><span class="sxs-lookup"><span data-stu-id="e8b21-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="e8b21-231">Трассировка = 0</span><span class="sxs-lookup"><span data-stu-id="e8b21-231">Trace = 0</span></span>

  <span data-ttu-id="e8b21-232">Для получения сведений, которые полезны только при отладке.</span><span class="sxs-lookup"><span data-stu-id="e8b21-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="e8b21-233">Эти сообщения могут содержать конфиденциальные данные приложения, поэтому их не следует включать в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e8b21-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="e8b21-234">*По умолчанию отключено.*</span><span class="sxs-lookup"><span data-stu-id="e8b21-234">*Disabled by default.*</span></span>

* <span data-ttu-id="e8b21-235">Отладка = 1</span><span class="sxs-lookup"><span data-stu-id="e8b21-235">Debug = 1</span></span>

  <span data-ttu-id="e8b21-236">Для получения сведений, которые полезны при разработке и отладке.</span><span class="sxs-lookup"><span data-stu-id="e8b21-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="e8b21-237">Пример. `Entering method Configure with flag set to true.` Включайте уровни ведения журналов `Debug` в рабочей среде только при устранении неполадок, так как такие журналы занимают много места.</span><span class="sxs-lookup"><span data-stu-id="e8b21-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="e8b21-238">Информация = 2</span><span class="sxs-lookup"><span data-stu-id="e8b21-238">Information = 2</span></span>

  <span data-ttu-id="e8b21-239">Для отслеживания общего потока работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="e8b21-240">Эти журналы обычно полезны в долгосрочной перспективе.</span><span class="sxs-lookup"><span data-stu-id="e8b21-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="e8b21-241">Пример: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="e8b21-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="e8b21-242">Предупреждение = 3</span><span class="sxs-lookup"><span data-stu-id="e8b21-242">Warning = 3</span></span>

  <span data-ttu-id="e8b21-243">Для нестандартных или непредвиденных событий, возникающих в потоке работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="e8b21-244">Это могут быть ошибки или другие условия, которые не приводят к остановке приложения, но которые нужно изучить.</span><span class="sxs-lookup"><span data-stu-id="e8b21-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="e8b21-245">Обработанные исключения являются распространенным условием использования уровня ведения журнала `Warning`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="e8b21-246">Пример: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="e8b21-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="e8b21-247">Ошибка = 4</span><span class="sxs-lookup"><span data-stu-id="e8b21-247">Error = 4</span></span>

  <span data-ttu-id="e8b21-248">Для ошибок и исключений, которые не могут быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="e8b21-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="e8b21-249">Эти сообщения указывают на сбой текущего действия или операции (например, текущего HTTP-запроса), а не на ошибку уровня приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="e8b21-250">Пример сообщения журнала: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="e8b21-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="e8b21-251">Критический = 5</span><span class="sxs-lookup"><span data-stu-id="e8b21-251">Critical = 5</span></span>

  <span data-ttu-id="e8b21-252">Для сбоев, которые требуют немедленного внимания.</span><span class="sxs-lookup"><span data-stu-id="e8b21-252">For failures that require immediate attention.</span></span> <span data-ttu-id="e8b21-253">Примеры: потеря данных, нехватка места на диске.</span><span class="sxs-lookup"><span data-stu-id="e8b21-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="e8b21-254">Уровень ведения журналов можно использовать для управления объемом выходных данных, записываемых на определенный носитель или в окно отображения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="e8b21-255">Пример:</span><span class="sxs-lookup"><span data-stu-id="e8b21-255">For example:</span></span>

* <span data-ttu-id="e8b21-256">В рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="e8b21-256">In production:</span></span>
  * <span data-ttu-id="e8b21-257">При ведении журнала на уровнях с `Trace` по `Information` создается большой объем подробных сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="e8b21-258">Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях с `Trace` по `Information` в хранилище данных с низкими затратами и большим объемом.</span><span class="sxs-lookup"><span data-stu-id="e8b21-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="e8b21-259">Ведение журнала на уровнях с `Warning` по `Critical` обычно приводит к записи меньшего количества сообщений меньшего размера.</span><span class="sxs-lookup"><span data-stu-id="e8b21-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="e8b21-260">Следовательно, затраты и лимиты хранилища не становятся проблемой, что приводит к большей гибкости при выборе хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="e8b21-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="e8b21-261">Во время разработки:</span><span class="sxs-lookup"><span data-stu-id="e8b21-261">During development:</span></span>
  * <span data-ttu-id="e8b21-262">Записывайте сообщения уровней с `Warning` по `Critical` на консоль.</span><span class="sxs-lookup"><span data-stu-id="e8b21-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="e8b21-263">Добавляйте сообщения уровней с `Trace` по `Information` при устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="e8b21-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="e8b21-264">В разделе [Фильтрация журналов](#log-filtering) далее в этой статье приводятся сведения о том, как контролировать уровни журнала, обрабатываемые поставщиком.</span><span class="sxs-lookup"><span data-stu-id="e8b21-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="e8b21-265">ASP.NET Core создает журналы для событий платформы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="e8b21-266">В примерах журнала ранее в этой статье не указывались журналы с уровнем ниже `Information`, поэтому журналы уровня `Debug` или `Trace` не создавались.</span><span class="sxs-lookup"><span data-stu-id="e8b21-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="e8b21-267">Ниже приведен пример журналов консоли, которые созданы при запуске примера приложения, настроенного на отображение журналов `Debug`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="e8b21-268">Идентификатор события журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-268">Log event ID</span></span>

<span data-ttu-id="e8b21-269">Каждый журнал может указывать *идентификатор события*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="e8b21-270">В примере приложения для этого используется локально определенный класс `LoggingEvents`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="e8b21-271">Идентификатор события связывает набор событий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-271">An event ID associates a set of events.</span></span> <span data-ttu-id="e8b21-272">Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.</span><span class="sxs-lookup"><span data-stu-id="e8b21-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="e8b21-273">Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить.</span><span class="sxs-lookup"><span data-stu-id="e8b21-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="e8b21-274">Поставщик Debug не отображает идентификаторы событий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="e8b21-275">Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:</span><span class="sxs-lookup"><span data-stu-id="e8b21-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="e8b21-276">Шаблон сообщения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-276">Log message template</span></span>

<span data-ttu-id="e8b21-277">Каждый журнал указывает шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-277">Each log specifies a message template.</span></span> <span data-ttu-id="e8b21-278">Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="e8b21-279">Используйте для заполнителей имена, а не числа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="e8b21-280">Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами.</span><span class="sxs-lookup"><span data-stu-id="e8b21-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="e8b21-281">В приведенном ниже коде обратите внимание на то, что имена параметров идут не по порядку в шаблоне сообщения:</span><span class="sxs-lookup"><span data-stu-id="e8b21-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="e8b21-282">Этот код создает сообщение журнала со значениями параметров в определенном порядке:</span><span class="sxs-lookup"><span data-stu-id="e8b21-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="e8b21-283">Платформа ведения журналов поддерживает такое поведение, чтобы поставщики ведения журнала могли реализовывать [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="e8b21-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="e8b21-284">Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="e8b21-285">Эта информация позволяет поставщикам ведения журналов хранить значения параметров как поля.</span><span class="sxs-lookup"><span data-stu-id="e8b21-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="e8b21-286">Предположим, например, что вызовы метода средства ведения журналов выглядят так:</span><span class="sxs-lookup"><span data-stu-id="e8b21-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="e8b21-287">Если вы отправляете журналы в Хранилище таблиц Azure, каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`, что упрощает выполнение запросов к данным журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="e8b21-288">Запрос может находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="e8b21-289">Ведение журнала исключений</span><span class="sxs-lookup"><span data-stu-id="e8b21-289">Logging exceptions</span></span>

<span data-ttu-id="e8b21-290">Методы средства ведения журнала имеют перегрузки, которые позволяют передавать исключение, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e8b21-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="e8b21-291">Разные поставщики обрабатывают сведения об исключениях по-разному.</span><span class="sxs-lookup"><span data-stu-id="e8b21-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="e8b21-292">Ниже приведен пример выходных данных поставщика Debug из приведенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="e8b21-293">Фильтрация журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-293">Log filtering</span></span>

<span data-ttu-id="e8b21-294">Можно указать минимальный уровень ведения журнала для определенного поставщика и категории или для всех поставщиков или всех категорий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="e8b21-295">Все журналы с уровнем ниже минимального не передаются этому поставщику, поэтому они не отображаются и не сохраняются.</span><span class="sxs-lookup"><span data-stu-id="e8b21-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="e8b21-296">Чтобы проигнорировать все журналы, укажите `LogLevel.None` в качестве минимального уровня ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="e8b21-297">`LogLevel.None` равно целочисленному значению 6, то есть больше, чем `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="e8b21-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="e8b21-298">Создание правил фильтрации в конфигурации</span><span class="sxs-lookup"><span data-stu-id="e8b21-298">Create filter rules in configuration</span></span>

<span data-ttu-id="e8b21-299">Код шаблона проектов вызывает `CreateDefaultBuilder` для настройки ведения журналов для поставщиков Console, Debug и EventSource (ASP.NET Core 2.2 или более поздняя версия).</span><span class="sxs-lookup"><span data-stu-id="e8b21-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="e8b21-300">Метод `CreateDefaultBuilder` настраивает ведение журнала для просмотра конфигурации в разделе `Logging`, как было описано [ранее в этой статье](#configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="e8b21-301">Данные конфигурации указывают минимальные уровни ведения журнала для каждого поставщика и категории, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e8b21-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="e8b21-302">Этот JSON создает шесть правил фильтрации: одно для поставщика Debug, четыре для поставщика Console и одно для всех поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e8b21-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="e8b21-303">При создании объекта `ILogger` для каждого поставщика выбирается только одно из этих правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="e8b21-304">Правила фильтрации в коде</span><span class="sxs-lookup"><span data-stu-id="e8b21-304">Filter rules in code</span></span>

<span data-ttu-id="e8b21-305">В следующем примере показано, как зарегистрировать в коде правила фильтрации:</span><span class="sxs-lookup"><span data-stu-id="e8b21-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="e8b21-306">Второй `AddFilter` указывает поставщика, Debug, используя имя его типа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="e8b21-307">Первый `AddFilter` применяется ко всем поставщикам, так как он не определяет тип поставщика.</span><span class="sxs-lookup"><span data-stu-id="e8b21-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="e8b21-308">Применение правил фильтрации</span><span class="sxs-lookup"><span data-stu-id="e8b21-308">How filtering rules are applied</span></span>

<span data-ttu-id="e8b21-309">Данные конфигурации и код `AddFilter`, приведенный в предыдущих примерах, создают правила, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e8b21-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="e8b21-310">Первые шесть взяты из примера конфигурации, а последние два — из примера кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="e8b21-311">Число</span><span class="sxs-lookup"><span data-stu-id="e8b21-311">Number</span></span> | <span data-ttu-id="e8b21-312">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e8b21-312">Provider</span></span>      | <span data-ttu-id="e8b21-313">Категории, которые начинаются с...</span><span class="sxs-lookup"><span data-stu-id="e8b21-313">Categories that begin with ...</span></span>          | <span data-ttu-id="e8b21-314">Минимальный уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="e8b21-315">1</span><span class="sxs-lookup"><span data-stu-id="e8b21-315">1</span></span>      | <span data-ttu-id="e8b21-316">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-316">Debug</span></span>         | <span data-ttu-id="e8b21-317">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-317">All categories</span></span>                          | <span data-ttu-id="e8b21-318">Сведения</span><span class="sxs-lookup"><span data-stu-id="e8b21-318">Information</span></span>       |
| <span data-ttu-id="e8b21-319">2</span><span class="sxs-lookup"><span data-stu-id="e8b21-319">2</span></span>      | <span data-ttu-id="e8b21-320">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-320">Console</span></span>       | <span data-ttu-id="e8b21-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="e8b21-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="e8b21-322">Предупреждение</span><span class="sxs-lookup"><span data-stu-id="e8b21-322">Warning</span></span>           |
| <span data-ttu-id="e8b21-323">3</span><span class="sxs-lookup"><span data-stu-id="e8b21-323">3</span></span>      | <span data-ttu-id="e8b21-324">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-324">Console</span></span>       | <span data-ttu-id="e8b21-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="e8b21-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="e8b21-326">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-326">Debug</span></span>             |
| <span data-ttu-id="e8b21-327">4</span><span class="sxs-lookup"><span data-stu-id="e8b21-327">4</span></span>      | <span data-ttu-id="e8b21-328">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-328">Console</span></span>       | <span data-ttu-id="e8b21-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="e8b21-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="e8b21-330">Ошибка</span><span class="sxs-lookup"><span data-stu-id="e8b21-330">Error</span></span>             |
| <span data-ttu-id="e8b21-331">5</span><span class="sxs-lookup"><span data-stu-id="e8b21-331">5</span></span>      | <span data-ttu-id="e8b21-332">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-332">Console</span></span>       | <span data-ttu-id="e8b21-333">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-333">All categories</span></span>                          | <span data-ttu-id="e8b21-334">Сведения</span><span class="sxs-lookup"><span data-stu-id="e8b21-334">Information</span></span>       |
| <span data-ttu-id="e8b21-335">6</span><span class="sxs-lookup"><span data-stu-id="e8b21-335">6</span></span>      | <span data-ttu-id="e8b21-336">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="e8b21-336">All providers</span></span> | <span data-ttu-id="e8b21-337">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-337">All categories</span></span>                          | <span data-ttu-id="e8b21-338">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-338">Debug</span></span>             |
| <span data-ttu-id="e8b21-339">7</span><span class="sxs-lookup"><span data-stu-id="e8b21-339">7</span></span>      | <span data-ttu-id="e8b21-340">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="e8b21-340">All providers</span></span> | <span data-ttu-id="e8b21-341">Система</span><span class="sxs-lookup"><span data-stu-id="e8b21-341">System</span></span>                                  | <span data-ttu-id="e8b21-342">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-342">Debug</span></span>             |
| <span data-ttu-id="e8b21-343">8</span><span class="sxs-lookup"><span data-stu-id="e8b21-343">8</span></span>      | <span data-ttu-id="e8b21-344">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-344">Debug</span></span>         | <span data-ttu-id="e8b21-345">Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e8b21-345">Microsoft</span></span>                               | <span data-ttu-id="e8b21-346">Трассировка</span><span class="sxs-lookup"><span data-stu-id="e8b21-346">Trace</span></span>             |

<span data-ttu-id="e8b21-347">При создании объекта `ILogger` объект `ILoggerFactory` выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="e8b21-348">Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="e8b21-349">Самое подходящее правило для каждой пары поставщика и категории выбирается из списка доступных правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="e8b21-350">При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:</span><span class="sxs-lookup"><span data-stu-id="e8b21-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="e8b21-351">Выберите все правила, которые соответствуют поставщику или его псевдониму.</span><span class="sxs-lookup"><span data-stu-id="e8b21-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="e8b21-352">Если ничего не найдено, выберите все правила с пустым поставщиком.</span><span class="sxs-lookup"><span data-stu-id="e8b21-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="e8b21-353">В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории.</span><span class="sxs-lookup"><span data-stu-id="e8b21-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="e8b21-354">Если ничего не найдено, выберите все правила, которые не указывают категорию.</span><span class="sxs-lookup"><span data-stu-id="e8b21-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="e8b21-355">Если выбрано несколько правил, примите **последнее**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="e8b21-356">Если правила не выбраны, используйте `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="e8b21-357">Предположим, у вас есть указанный выше список правил и вы создаете объект `ILogger` для категории Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine:</span><span class="sxs-lookup"><span data-stu-id="e8b21-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="e8b21-358">К поставщику Debug применяются правила 1, 6 и 8.</span><span class="sxs-lookup"><span data-stu-id="e8b21-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="e8b21-359">Правило 8 является наиболее подходящим, поэтому выбрано оно.</span><span class="sxs-lookup"><span data-stu-id="e8b21-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="e8b21-360">К поставщику отладки применяются правила 3, 4, 5 и 6.</span><span class="sxs-lookup"><span data-stu-id="e8b21-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="e8b21-361">Правило 3 является наиболее подходящим.</span><span class="sxs-lookup"><span data-stu-id="e8b21-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="e8b21-362">Полученный в результате экземпляр `ILogger` отправляет журналы уровня `Trace` и выше в поставщик Debug.</span><span class="sxs-lookup"><span data-stu-id="e8b21-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="e8b21-363">Журналы уровня `Debug` и выше отправляются в поставщик Console.</span><span class="sxs-lookup"><span data-stu-id="e8b21-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="e8b21-364">Псевдонимы поставщиков</span><span class="sxs-lookup"><span data-stu-id="e8b21-364">Provider aliases</span></span>

<span data-ttu-id="e8b21-365">Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="e8b21-366">Для встроенных поставщиков используйте следующие псевдонимы:</span><span class="sxs-lookup"><span data-stu-id="e8b21-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="e8b21-367">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-367">Console</span></span>
* <span data-ttu-id="e8b21-368">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-368">Debug</span></span>
* <span data-ttu-id="e8b21-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-369">EventSource</span></span>
* <span data-ttu-id="e8b21-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-370">EventLog</span></span>
* <span data-ttu-id="e8b21-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-371">TraceSource</span></span>
* <span data-ttu-id="e8b21-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e8b21-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="e8b21-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e8b21-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="e8b21-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e8b21-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="e8b21-375">Минимальный уровень по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e8b21-375">Default minimum level</span></span>

<span data-ttu-id="e8b21-376">Существует параметр минимального уровня, который применяется, только если к определенному поставщику или категории не подходят правила из конфигурации или кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="e8b21-377">В следующем примере показано, как задать минимальный уровень:</span><span class="sxs-lookup"><span data-stu-id="e8b21-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="e8b21-378">Если минимальный уровень не задан явным образом, используется значение по умолчанию — `Information`, означающее, что журналы `Trace` и `Debug` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="e8b21-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="e8b21-379">Функции фильтрации</span><span class="sxs-lookup"><span data-stu-id="e8b21-379">Filter functions</span></span>

<span data-ttu-id="e8b21-380">Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила.</span><span class="sxs-lookup"><span data-stu-id="e8b21-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="e8b21-381">Код в функции имеет доступ к типу поставщика, категории и уровню ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="e8b21-382">Пример:</span><span class="sxs-lookup"><span data-stu-id="e8b21-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="e8b21-383">Системные категории и уровни</span><span class="sxs-lookup"><span data-stu-id="e8b21-383">System categories and levels</span></span>

<span data-ttu-id="e8b21-384">Ниже приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:</span><span class="sxs-lookup"><span data-stu-id="e8b21-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="e8b21-385">Категория</span><span class="sxs-lookup"><span data-stu-id="e8b21-385">Category</span></span>                            | <span data-ttu-id="e8b21-386">Примечания</span><span class="sxs-lookup"><span data-stu-id="e8b21-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="e8b21-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="e8b21-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="e8b21-388">Общая диагностика ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="e8b21-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="e8b21-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="e8b21-390">Распознанные, найденные и использованные ключи.</span><span class="sxs-lookup"><span data-stu-id="e8b21-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="e8b21-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="e8b21-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="e8b21-392">Разрешенные узлы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-392">Hosts allowed.</span></span> |
| <span data-ttu-id="e8b21-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="e8b21-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="e8b21-394">Время начала и длительность выполнения HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="e8b21-395">Определение загруженных начальных сборок размещения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="e8b21-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="e8b21-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="e8b21-397">Диагностика MVC и Razor.</span><span class="sxs-lookup"><span data-stu-id="e8b21-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="e8b21-398">Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия.</span><span class="sxs-lookup"><span data-stu-id="e8b21-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="e8b21-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="e8b21-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="e8b21-400">Перенаправление соответствующих сведений.</span><span class="sxs-lookup"><span data-stu-id="e8b21-400">Route matching information.</span></span> |
| <span data-ttu-id="e8b21-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="e8b21-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="e8b21-402">Запуск, остановка и сохранение ответов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="e8b21-403">Сведения о сертификате HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e8b21-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="e8b21-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="e8b21-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="e8b21-405">Обработанные файлы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-405">Files served.</span></span> |
| <span data-ttu-id="e8b21-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="e8b21-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="e8b21-407">Общая диагностика Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="e8b21-408">Действия и конфигурация базы данных, обнаружение изменений, переносы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="e8b21-409">Области журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-409">Log scopes</span></span>

 <span data-ttu-id="e8b21-410">*Область* может группировать набор логических операций.</span><span class="sxs-lookup"><span data-stu-id="e8b21-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="e8b21-411">Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора.</span><span class="sxs-lookup"><span data-stu-id="e8b21-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="e8b21-412">Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.</span><span class="sxs-lookup"><span data-stu-id="e8b21-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="e8b21-413">Область — это тип `IDisposable`, возвращаемый методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> и действующий до удаления.</span><span class="sxs-lookup"><span data-stu-id="e8b21-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="e8b21-414">Используйте область, заключив вызовы средства ведения журналов в блок `using`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="e8b21-415">Следующий код предоставляет области для поставщика Console:</span><span class="sxs-lookup"><span data-stu-id="e8b21-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="e8b21-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="e8b21-417">Для включения ведения журнала на уровне области требуется параметр `IncludeScopes` средства ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="e8b21-418">Сведения о настройках см. в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="e8b21-419">Каждое сообщение журнала содержит ограниченную информацию:</span><span class="sxs-lookup"><span data-stu-id="e8b21-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="e8b21-420">Встроенные поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-420">Built-in logging providers</span></span>

<span data-ttu-id="e8b21-421">В состав ASP.NET Core входят следующие поставщики:</span><span class="sxs-lookup"><span data-stu-id="e8b21-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="e8b21-422">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="e8b21-423">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="e8b21-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="e8b21-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="e8b21-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="e8b21-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e8b21-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e8b21-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e8b21-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e8b21-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e8b21-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="e8b21-430">Сведения о stdout и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="e8b21-431">Поставщик Console</span><span class="sxs-lookup"><span data-stu-id="e8b21-431">Console provider</span></span>

<span data-ttu-id="e8b21-432">Пакет поставщика [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) отправляет выходные данные журнала на консоль.</span><span class="sxs-lookup"><span data-stu-id="e8b21-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="e8b21-433">Чтобы просмотреть выходные данные ведения журналов в консоли, откройте командную строку, перейдите в папку проекта и запустите приведенную ниже команду:</span><span class="sxs-lookup"><span data-stu-id="e8b21-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="e8b21-434">Поставщик Debug</span><span class="sxs-lookup"><span data-stu-id="e8b21-434">Debug provider</span></span>

<span data-ttu-id="e8b21-435">Пакет поставщика [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) записывает выходные данные журналов с помощью класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (вызовов метода `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="e8b21-436">В Linux этот поставщик записывает журналы в каталог */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="e8b21-437">Поставщик источника событий</span><span class="sxs-lookup"><span data-stu-id="e8b21-437">Event Source provider</span></span>

<span data-ttu-id="e8b21-438">Пакет поставщика [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) записывает данные в источник событий на кросс-платформенный сервер с именем `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="e8b21-439">В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="e8b21-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="e8b21-440">Поставщик источника событий добавляется автоматически при вызове `CreateDefaultBuilder` для сборки узла.</span><span class="sxs-lookup"><span data-stu-id="e8b21-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="e8b21-441">Средства трассировки dotnet</span><span class="sxs-lookup"><span data-stu-id="e8b21-441">dotnet trace tooling</span></span>

<span data-ttu-id="e8b21-442">Средство [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) — это универсальное кроссплатформенное средство командной строки, которое выполняет сбор трассировок .NET Core для запущенного процесса.</span><span class="sxs-lookup"><span data-stu-id="e8b21-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="e8b21-443">Средство собирает данные поставщика <xref:Microsoft.Extensions.Logging.EventSource> с помощью <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="e8b21-444">Установите средства трассировки dotnet с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="e8b21-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="e8b21-445">Используйте средства трассировки dotnet, чтобы получить трассировку из приложения:</span><span class="sxs-lookup"><span data-stu-id="e8b21-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="e8b21-446">Если приложение не создает узел с `CreateDefaultBuilder`, добавьте [Поставщика источника событий](#event-source-provider) в конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="e8b21-447">Запустите приложение с помощью команды `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="e8b21-448">Определите идентификатор процесса (PID) приложения .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e8b21-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="e8b21-449">В Windows воспользуйтесь одним из перечисленных ниже подходов:</span><span class="sxs-lookup"><span data-stu-id="e8b21-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="e8b21-450">Диспетчер задач (CTRL+ALT+DEL)</span><span class="sxs-lookup"><span data-stu-id="e8b21-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="e8b21-451">Команда tasklist</span><span class="sxs-lookup"><span data-stu-id="e8b21-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="e8b21-452">Команда Powershell Get-Process</span><span class="sxs-lookup"><span data-stu-id="e8b21-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="e8b21-453">В Linux используйте [команду pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="e8b21-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="e8b21-454">Найдите идентификатор процесса, имя которого совпадает с именем сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="e8b21-455">Выполните команду `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="e8b21-456">Общий синтаксис команды:</span><span class="sxs-lookup"><span data-stu-id="e8b21-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="e8b21-457">При использовании командной оболочки PowerShell заключите значение `--providers` в одинарные кавычки (`'`):</span><span class="sxs-lookup"><span data-stu-id="e8b21-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="e8b21-458">На платформах, отличных от Windows, добавьте параметр `-f speedscope`, чтобы изменить формат выходного файла трассировки на `speedscope`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="e8b21-459">Ключевое слово</span><span class="sxs-lookup"><span data-stu-id="e8b21-459">Keyword</span></span> | <span data-ttu-id="e8b21-460">Описание</span><span class="sxs-lookup"><span data-stu-id="e8b21-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="e8b21-461">1</span><span class="sxs-lookup"><span data-stu-id="e8b21-461">1</span></span>       | <span data-ttu-id="e8b21-462">Занесите в журнал метасобытия о `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="e8b21-463">Не заносит в журнал события из `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="e8b21-464">2</span><span class="sxs-lookup"><span data-stu-id="e8b21-464">2</span></span>       | <span data-ttu-id="e8b21-465">Включает событие `Message` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="e8b21-466">Предоставляет сведения в исходном виде (без форматирования).</span><span class="sxs-lookup"><span data-stu-id="e8b21-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="e8b21-467">4</span><span class="sxs-lookup"><span data-stu-id="e8b21-467">4</span></span>       | <span data-ttu-id="e8b21-468">Включает событие `FormatMessage` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="e8b21-469">Предоставляет сведения в виде отформатированной строки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="e8b21-470">8</span><span class="sxs-lookup"><span data-stu-id="e8b21-470">8</span></span>       | <span data-ttu-id="e8b21-471">Включает событие `MessageJson` при вызове `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="e8b21-472">Предоставляет представление аргументов в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="e8b21-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="e8b21-473">Уровень события</span><span class="sxs-lookup"><span data-stu-id="e8b21-473">Event Level</span></span> | <span data-ttu-id="e8b21-474">Описание</span><span class="sxs-lookup"><span data-stu-id="e8b21-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="e8b21-475">0</span><span class="sxs-lookup"><span data-stu-id="e8b21-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="e8b21-476">1</span><span class="sxs-lookup"><span data-stu-id="e8b21-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="e8b21-477">2</span><span class="sxs-lookup"><span data-stu-id="e8b21-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="e8b21-478">3</span><span class="sxs-lookup"><span data-stu-id="e8b21-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="e8b21-479">4</span><span class="sxs-lookup"><span data-stu-id="e8b21-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="e8b21-480">5</span><span class="sxs-lookup"><span data-stu-id="e8b21-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="e8b21-481">Записи `FilterSpecs` для `{Logger Category}` и `{Event Level}` представляют дополнительные условия фильтрации журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="e8b21-482">Отдельные записи `FilterSpecs` разделяются точкой с запятой (`;`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="e8b21-483">Пример использования командной оболочки Windows (**не** одинарные кавычки вокруг значения `--providers`):</span><span class="sxs-lookup"><span data-stu-id="e8b21-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="e8b21-484">Предыдущая команда активирует:</span><span class="sxs-lookup"><span data-stu-id="e8b21-484">The preceding command activates:</span></span>

   * <span data-ttu-id="e8b21-485">Средство ведения журнала источника событий для создания форматированных строк (`4`) для ошибок (`2`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="e8b21-486">Ведение журнала `Microsoft.AspNetCore.Hosting` на уровне ведения журнала `Informational` (`4`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="e8b21-487">Остановите средства трассировки dotnet, нажав клавишу ENTER или CTRL+C.</span><span class="sxs-lookup"><span data-stu-id="e8b21-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="e8b21-488">Трассировка сохраняется с именем *trace.nettrace* в папке, в которой выполняется команда `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="e8b21-489">Откройте трассировку с помощью [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="e8b21-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="e8b21-490">Откройте файл *trace.nettrace* и изучите события трассировки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="e8b21-491">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e8b21-491">For more information, see:</span></span>

* <span data-ttu-id="e8b21-492">[Трассировка для программы анализа производительности (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (документация по .NET Core)</span><span class="sxs-lookup"><span data-stu-id="e8b21-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="e8b21-493">[Трассировка для программы анализа производительности (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (документация по репозиторию GitHub dotnet/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="e8b21-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="e8b21-494">[Класс LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (обозреватель API .NET)</span><span class="sxs-lookup"><span data-stu-id="e8b21-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="e8b21-495">[Источник ссылки LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Чтобы получить источник ссылки для другой версии, измените ветку на `release/{Version}`, где `{Version}` — это нужная версия ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="e8b21-496">[Perfview](#perfview) &ndash; полезный инструмент для просмотра трассировок источника событий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="e8b21-497">Perfview</span><span class="sxs-lookup"><span data-stu-id="e8b21-497">Perfview</span></span>

<span data-ttu-id="e8b21-498">Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="e8b21-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="e8b21-499">Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="e8b21-500">Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="e8b21-501">(Не пропустите звездочку в начале строки.)</span><span class="sxs-lookup"><span data-stu-id="e8b21-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Дополнительные поставщики Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="e8b21-503">Поставщик Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-503">Windows EventLog provider</span></span>

<span data-ttu-id="e8b21-504">Пакет поставщика [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) отправляет выходные данные журнала в журнал событий Windows.</span><span class="sxs-lookup"><span data-stu-id="e8b21-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="e8b21-505">[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="e8b21-506">Если `null` или не указан, используются следующие параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="e8b21-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="e8b21-507">`LogName` &ndash; "Приложение"</span><span class="sxs-lookup"><span data-stu-id="e8b21-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="e8b21-508">`SourceName` &ndash; "Среда выполнения .NET"</span><span class="sxs-lookup"><span data-stu-id="e8b21-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="e8b21-509">`MachineName` &ndash; (локальный компьютер)</span><span class="sxs-lookup"><span data-stu-id="e8b21-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="e8b21-510">События регистрируются для [уровня предупреждения и более высоких уровней](#log-level).</span><span class="sxs-lookup"><span data-stu-id="e8b21-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="e8b21-511">Чтобы регистрировать события с уровнем ниже `Warning`, следует явно задать уровень ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="e8b21-512">Например, добавьте следующий код в файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="e8b21-513">Поставщик TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-513">TraceSource provider</span></span>

<span data-ttu-id="e8b21-514">Пакет поставщика [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) использует библиотеки и поставщики <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="e8b21-515">[Перегрузки AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) позволяют передавать исходный параметр и прослушиватель трассировки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="e8b21-516">Чтобы использовать этот поставщик, приложение должно выполняться в .NET Framework (а не в .NET Core).</span><span class="sxs-lookup"><span data-stu-id="e8b21-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="e8b21-517">Поставщик позволяет перенаправлять сообщения различным [прослушивателям](/dotnet/framework/debug-trace-profile/trace-listeners), например <xref:System.Diagnostics.TextWriterTraceListener>, который используется в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="e8b21-518">Поставщик службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-518">Azure App Service provider</span></span>

<span data-ttu-id="e8b21-519">Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="e8b21-520">Пакет поставщика не включен в общую платформу.</span><span class="sxs-lookup"><span data-stu-id="e8b21-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="e8b21-521">Чтобы использовать поставщик, добавьте пакет поставщика в проект.</span><span class="sxs-lookup"><span data-stu-id="e8b21-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="e8b21-522">Для настройки параметров поставщика используйте <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> и <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e8b21-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="e8b21-523">При развертывании в приложение Службы приложений ваше приложение использует параметры в разделе [Журналы Службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) на странице **Служба приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="e8b21-524">При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:</span><span class="sxs-lookup"><span data-stu-id="e8b21-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="e8b21-525">**Ведение журнала приложения (файловая система)** ;</span><span class="sxs-lookup"><span data-stu-id="e8b21-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="e8b21-526">**Ведение журнала приложения (BLOB-объект)** .</span><span class="sxs-lookup"><span data-stu-id="e8b21-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="e8b21-527">По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="e8b21-528">Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2.</span><span class="sxs-lookup"><span data-stu-id="e8b21-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="e8b21-529">Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="e8b21-530">Поставщик работает только при выполнении проекта в среде Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="e8b21-531">Он не функционирует при запуске проекта в локальной среде &mdash;(то есть не выполняет запись в локальные файлы или в локальное хранилище разработки для больших двоичных объектов).</span><span class="sxs-lookup"><span data-stu-id="e8b21-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="e8b21-532">Потоковая передача журналов Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-532">Azure log streaming</span></span>

<span data-ttu-id="e8b21-533">Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="e8b21-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="e8b21-534">сервер приложений;</span><span class="sxs-lookup"><span data-stu-id="e8b21-534">The app server</span></span>
* <span data-ttu-id="e8b21-535">веб-сервер;</span><span class="sxs-lookup"><span data-stu-id="e8b21-535">The web server</span></span>
* <span data-ttu-id="e8b21-536">трассировка неудачно завершенных запросов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-536">Failed request tracing</span></span>

<span data-ttu-id="e8b21-537">Настройка потоковой передачи журналов Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="e8b21-538">Со страницы портала приложения перейдите на страницу **Журналы Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="e8b21-539">**Включите** параметр **Ведение журнала приложения (файловая система)** .</span><span class="sxs-lookup"><span data-stu-id="e8b21-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="e8b21-540">Выберите **уровень** ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-540">Choose the log **Level**.</span></span> <span data-ttu-id="e8b21-541">Этот параметр применяется только к потоковой передаче журналов Azure, а не к другим поставщикам ведения журнала в приложении.</span><span class="sxs-lookup"><span data-stu-id="e8b21-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="e8b21-542">Перейдите на страницу **Поток журналов**, чтобы просмотреть сообщения приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="e8b21-543">Они записываются в журнал приложением через интерфейс `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="e8b21-544">Ведение журнала трассировки Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="e8b21-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="e8b21-545">Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="e8b21-546">Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии.</span><span class="sxs-lookup"><span data-stu-id="e8b21-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="e8b21-547">Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="e8b21-548">Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="e8b21-549">Если вы используете этот пакет, пакет поставщика устанавливать не нужно.</span><span class="sxs-lookup"><span data-stu-id="e8b21-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="e8b21-550">Не используйте пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web), который предназначен для ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="e8b21-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="e8b21-551">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="e8b21-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e8b21-552">Общие сведения об Application Insights</span><span class="sxs-lookup"><span data-stu-id="e8b21-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="e8b21-553">[Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="e8b21-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.</span><span class="sxs-lookup"><span data-stu-id="e8b21-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="e8b21-555">[Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="e8b21-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="e8b21-556">[Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="e8b21-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="e8b21-557">Сторонние поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-557">Third-party logging providers</span></span>

<span data-ttu-id="e8b21-558">Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e8b21-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="e8b21-559">[ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="e8b21-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="e8b21-561">[JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));</span><span class="sxs-lookup"><span data-stu-id="e8b21-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="e8b21-562">[KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="e8b21-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="e8b21-563">[Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="e8b21-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="e8b21-564">[Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-565">[NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-566">[Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e8b21-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="e8b21-567">[Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).</span><span class="sxs-lookup"><span data-stu-id="e8b21-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="e8b21-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e8b21-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="e8b21-569">Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="e8b21-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="e8b21-570">Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:</span><span class="sxs-lookup"><span data-stu-id="e8b21-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="e8b21-571">Добавьте пакет NuGet в проект.</span><span class="sxs-lookup"><span data-stu-id="e8b21-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="e8b21-572">Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="e8b21-573">Дополнительные сведения см. в документации по каждому поставщику.</span><span class="sxs-lookup"><span data-stu-id="e8b21-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="e8b21-574">Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="e8b21-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8b21-575">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e8b21-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8b21-576">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="e8b21-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e8b21-577">.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="e8b21-578">В этой статье описано, как использовать в коде API ведения журналов, работающего со встроенными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="e8b21-579">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8b21-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="e8b21-580">Добавление поставщиков</span><span class="sxs-lookup"><span data-stu-id="e8b21-580">Add providers</span></span>

<span data-ttu-id="e8b21-581">Поставщик ведения журналов отображает или сохраняет журналы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="e8b21-582">Например, поставщик Console отображает журналы на консоли, а поставщик Azure Application Insights может сохранить их в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="e8b21-583">Журналы могут отправляться в несколько назначений после добавления нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e8b21-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="e8b21-584">Для добавления поставщика вызовите метод расширения `Add{provider name}` поставщика в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="e8b21-585">В указанном выше коде необходимо указать ссылки на `Microsoft.Extensions.Logging` и `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="e8b21-586">Шаблон проекта по умолчанию вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, который добавляет следующие поставщики ведения журналов:</span><span class="sxs-lookup"><span data-stu-id="e8b21-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="e8b21-587">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-587">Console</span></span>
* <span data-ttu-id="e8b21-588">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-588">Debug</span></span>
* <span data-ttu-id="e8b21-589">EventSource (начиная с версии ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="e8b21-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="e8b21-590">Если вы используете `CreateDefaultBuilder`, поставщики по умолчанию можно заменить собственными поставщиками.</span><span class="sxs-lookup"><span data-stu-id="e8b21-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="e8b21-591">Вызовите <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> и добавьте требуемые поставщики.</span><span class="sxs-lookup"><span data-stu-id="e8b21-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="e8b21-592">См. сведения о [встроенных](#built-in-logging-providers) и [сторонних](#third-party-logging-providers) поставщиках ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="e8b21-593">Создание журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-593">Create logs</span></span>

<span data-ttu-id="e8b21-594">Чтобы создать журналы, используйте объект <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="e8b21-595">В веб-приложении или размещенной службе получите `ILogger` посредством внедрения зависимостей (DI).</span><span class="sxs-lookup"><span data-stu-id="e8b21-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="e8b21-596">В консольных приложениях без размещения используйте `LoggerFactory` для создания `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="e8b21-597">В приведенном ниже примере для ASP.NET Core создается средство ведения журнала с категорией `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="e8b21-598">*Категория* ведения журналов представляет строку, которая связана с каждым журналом.</span><span class="sxs-lookup"><span data-stu-id="e8b21-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="e8b21-599">Экземпляр `ILogger<T>`, предоставляемый внедрением зависимостей, создает журналы с полным именем типа `T` в качестве категории.</span><span class="sxs-lookup"><span data-stu-id="e8b21-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="e8b21-600">В приведенных ниже примерах ASP.NET Core и консольного приложения средство ведения журнала используется для создания журналов с уровнем `Information`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="e8b21-601">*Уровень* ведения журналов определяет степень серьезности или важности записанного в журнал события.</span><span class="sxs-lookup"><span data-stu-id="e8b21-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="e8b21-602">[Уровни](#log-level) и [категории](#log-category) рассматриваются подробнее далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="e8b21-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="e8b21-603">Создание журналов в классе Startup</span><span class="sxs-lookup"><span data-stu-id="e8b21-603">Create logs in Startup</span></span>

<span data-ttu-id="e8b21-604">Для записи журналов в классе `Startup` включите параметр `ILogger` в сигнатуру конструктора:</span><span class="sxs-lookup"><span data-stu-id="e8b21-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="e8b21-605">Создание журналов в классе Program</span><span class="sxs-lookup"><span data-stu-id="e8b21-605">Create logs in the Program class</span></span>

<span data-ttu-id="e8b21-606">Для записи журналов в классе `Program` получите экземпляр `ILogger` путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="e8b21-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="e8b21-607">Ведение журнала во время создания узла не поддерживается напрямую.</span><span class="sxs-lookup"><span data-stu-id="e8b21-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="e8b21-608">Однако можно использовать отдельное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-608">However, a separate logger can be used.</span></span> <span data-ttu-id="e8b21-609">В следующем примере для входа в `CreateWebHostBuilder` используется средство ведения журнала [Serilog](https://serilog.net/).</span><span class="sxs-lookup"><span data-stu-id="e8b21-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="e8b21-610">`AddSerilog` использует статическую конфигурацию, указанную в `Log.Logger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="e8b21-611">Асинхронные методы ведения журналов не выполняются</span><span class="sxs-lookup"><span data-stu-id="e8b21-611">No asynchronous logger methods</span></span>

<span data-ttu-id="e8b21-612">Скорость ведения журналов не должна влиять на производительность выполнения асинхронного кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="e8b21-613">Если хранилище данных, предназначенное для регистрации сообщений журнала, работает медленно, сначала записывайте эти сообщения в быстродействующее хранилище,</span><span class="sxs-lookup"><span data-stu-id="e8b21-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="e8b21-614">а затем перемещайте их в медленное хранилище.</span><span class="sxs-lookup"><span data-stu-id="e8b21-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="e8b21-615">Например, если вы входите в SQL Server, вам не нужно делать это непосредственно в методе `Log`, так как методы `Log` являются синхронными.</span><span class="sxs-lookup"><span data-stu-id="e8b21-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="e8b21-616">Вместо этого синхронно добавьте сообщения журнала в очередь в памяти, и фоновый рабочий поток извлечет сообщения из очереди для выполнения асинхронных операций передачи данных в SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e8b21-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="e8b21-617">Дополнительные сведения см. [здесь](https://github.com/dotnet/AspNetCore.Docs/issues/11801) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="e8b21-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="e8b21-618">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e8b21-618">Configuration</span></span>

<span data-ttu-id="e8b21-619">Конфигурацию поставщика ведения журналов предоставляет как минимум один поставщик конфигураций:</span><span class="sxs-lookup"><span data-stu-id="e8b21-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="e8b21-620">Форматы файлов (INI, JSON и XML).</span><span class="sxs-lookup"><span data-stu-id="e8b21-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="e8b21-621">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-621">Command-line arguments.</span></span>
* <span data-ttu-id="e8b21-622">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e8b21-622">Environment variables.</span></span>
* <span data-ttu-id="e8b21-623">Объекты .NET в памяти.</span><span class="sxs-lookup"><span data-stu-id="e8b21-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="e8b21-624">Незашифрованное хранилище [Secret Manager](xref:security/app-secrets) (Диспетчер секретов).</span><span class="sxs-lookup"><span data-stu-id="e8b21-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="e8b21-625">Зашифрованное пользовательское хранилище, например [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="e8b21-626">Пользовательские поставщики (установленные или созданные).</span><span class="sxs-lookup"><span data-stu-id="e8b21-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="e8b21-627">Например, конфигурацию ведения журналов обычно предоставляет раздел `Logging` в файле параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="e8b21-628">В следующем примере показано содержимое типичного файла *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="e8b21-629">Свойство `Logging` может иметь свойство `LogLevel` и свойства поставщика журналов (здесь — Console).</span><span class="sxs-lookup"><span data-stu-id="e8b21-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="e8b21-630">Свойство `LogLevel` в разделе `Logging` указывает минимальный [уровень](#log-level) журнала для выбранных категорий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="e8b21-631">В этом примере категории `System` и `Microsoft` записываются на уровне `Information`, а остальные — на уровне `Debug`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="e8b21-632">Другие свойства в разделе `Logging` определяют поставщиков ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="e8b21-633">Пример приведен для поставщика Console.</span><span class="sxs-lookup"><span data-stu-id="e8b21-633">The example is for the Console provider.</span></span> <span data-ttu-id="e8b21-634">Если поставщик поддерживает [области журналов](#log-scopes), `IncludeScopes` определяет, включены ли они.</span><span class="sxs-lookup"><span data-stu-id="e8b21-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="e8b21-635">Свойство поставщика (например, `Console` в этом примере) также определять свойство `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="e8b21-636">`LogLevel` под поставщиком указывает уровни журнала для этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="e8b21-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="e8b21-637">Если уровни указаны в `Logging.{providername}.LogLevel`, они не переопределяют ничего из того, что задано в `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="e8b21-638">API ведения журнала не включает сценарий для изменения уровней журнала во время работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="e8b21-639">Однако некоторые поставщики конфигурации могут перезагружать конфигурацию, что немедленно влияет на конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="e8b21-640">Например, [Поставщик конфигурации файлов](xref:fundamentals/configuration/index#file-configuration-provider), который добавляется `CreateDefaultBuilder` для чтения файлов параметров, по умолчанию перезагружает конфигурацию ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="e8b21-641">Если конфигурация изменяется в коде во время выполнения приложения, приложение может вызвать [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*), чтобы обновить конфигурацию ведения журнала приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="e8b21-642">Сведения о реализации поставщиков конфигураций см. в статье <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="e8b21-643">Пример выходных данных ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-643">Sample logging output</span></span>

<span data-ttu-id="e8b21-644">В примере кода из предыдущего раздела журналы появляются в консоли после запуска приложения из командной строки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="e8b21-645">Ниже приведен пример выходных данных консоли:</span><span class="sxs-lookup"><span data-stu-id="e8b21-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="e8b21-646">Предыдущие журналы созданы путем отправки HTTP-запроса Get к примеру приложения по адресу `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="e8b21-647">Ниже приведен пример этих журналов в том виде, в каком они отображаются в окне отладки при запуске примера приложения в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e8b21-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="e8b21-648">Журналы, которые созданы путем вызовов `ILogger`, как показано в предыдущем разделе, начинаются с TodoApi.</span><span class="sxs-lookup"><span data-stu-id="e8b21-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="e8b21-649">Журналы, которые начинаются с категорий Microsoft, входят в код платформы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="e8b21-650">В ASP.NET Core и коде приложения используется один и тот же API ведения журналов и одни и те же поставщики.</span><span class="sxs-lookup"><span data-stu-id="e8b21-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="e8b21-651">В оставшейся части этой статьи рассматриваются некоторые сведения и параметры для ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="e8b21-652">Пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="e8b21-652">NuGet packages</span></span>

<span data-ttu-id="e8b21-653">Интерфейсы `ILogger` и `ILoggerFactory` находятся в [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), а их реализации по умолчанию — в [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="e8b21-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="e8b21-654">Категория журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-654">Log category</span></span>

<span data-ttu-id="e8b21-655">При создании объекта `ILogger` для него указывается *категория*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="e8b21-656">Эта категория входит в состав каждого сообщения журнала, создаваемого этим экземпляром `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="e8b21-657">Категория может быть любой строкой, обычно используется имя класса, например TodoApi.Controllers.TodoController.</span><span class="sxs-lookup"><span data-stu-id="e8b21-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="e8b21-658">Используйте `ILogger<T>` для получения экземпляра `ILogger`, который использует полное имя типа `T` в качестве категории:</span><span class="sxs-lookup"><span data-stu-id="e8b21-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="e8b21-659">Чтобы явно указать категорию, вызовите `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="e8b21-660">Использование `ILogger<T>` эквивалентно вызову `CreateLogger` с полным именем типа `T`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="e8b21-661">Уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-661">Log level</span></span>

<span data-ttu-id="e8b21-662">Каждый журнал задает значение <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="e8b21-663">Уровень ведения журналов означает степень серьезности или важности.</span><span class="sxs-lookup"><span data-stu-id="e8b21-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="e8b21-664">Например, можно создать журнал `Information` при нормальном завершении метода и журнал `Warning`, если метод возвращает код состояния *404 — не найдено*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="e8b21-665">Следующий код создает журналы `Information`и `Warning`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="e8b21-666">В коде выше первый параметр — это [идентификатор события журнала](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="e8b21-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="e8b21-667">Второй параметр — это шаблон сообщения с заполнителями для значений аргументов, предоставляемых оставшимися параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="e8b21-668">Параметры метода рассматриваются более подробно в разделе, посвященном [шаблону сообщений](#log-message-template) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="e8b21-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="e8b21-669">Методы журналов, которые содержат уровень в имени метода (например, `LogInformation` и `LogWarning`), являются [методами расширения для ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="e8b21-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="e8b21-670">Эти методы вызывают метод `Log`, принимающий параметр `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="e8b21-671">Метод `Log`, в отличие от этих методов расширения, можно вызывать напрямую, однако его синтаксис довольно сложен.</span><span class="sxs-lookup"><span data-stu-id="e8b21-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="e8b21-672">См. сведения о <xref:Microsoft.Extensions.Logging.ILogger> и [исходном коде расширений средства ведения журналов](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e8b21-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="e8b21-673">В ASP.NET Core определяются следующие уровни ведения журналов, упорядоченные по возрастанию уровней серьезности.</span><span class="sxs-lookup"><span data-stu-id="e8b21-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="e8b21-674">Трассировка = 0</span><span class="sxs-lookup"><span data-stu-id="e8b21-674">Trace = 0</span></span>

  <span data-ttu-id="e8b21-675">Для получения сведений, которые полезны только при отладке.</span><span class="sxs-lookup"><span data-stu-id="e8b21-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="e8b21-676">Эти сообщения могут содержать конфиденциальные данные приложения, поэтому их не следует включать в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e8b21-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="e8b21-677">*По умолчанию отключено.*</span><span class="sxs-lookup"><span data-stu-id="e8b21-677">*Disabled by default.*</span></span>

* <span data-ttu-id="e8b21-678">Отладка = 1</span><span class="sxs-lookup"><span data-stu-id="e8b21-678">Debug = 1</span></span>

  <span data-ttu-id="e8b21-679">Для получения сведений, которые полезны при разработке и отладке.</span><span class="sxs-lookup"><span data-stu-id="e8b21-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="e8b21-680">Пример. `Entering method Configure with flag set to true.` Включайте уровни ведения журналов `Debug` в рабочей среде только при устранении неполадок, так как такие журналы занимают много места.</span><span class="sxs-lookup"><span data-stu-id="e8b21-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="e8b21-681">Информация = 2</span><span class="sxs-lookup"><span data-stu-id="e8b21-681">Information = 2</span></span>

  <span data-ttu-id="e8b21-682">Для отслеживания общего потока работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="e8b21-683">Эти журналы обычно полезны в долгосрочной перспективе.</span><span class="sxs-lookup"><span data-stu-id="e8b21-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="e8b21-684">Пример: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="e8b21-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="e8b21-685">Предупреждение = 3</span><span class="sxs-lookup"><span data-stu-id="e8b21-685">Warning = 3</span></span>

  <span data-ttu-id="e8b21-686">Для нестандартных или непредвиденных событий, возникающих в потоке работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="e8b21-687">Это могут быть ошибки или другие условия, которые не приводят к остановке приложения, но которые нужно изучить.</span><span class="sxs-lookup"><span data-stu-id="e8b21-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="e8b21-688">Обработанные исключения являются распространенным условием использования уровня ведения журнала `Warning`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="e8b21-689">Пример: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="e8b21-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="e8b21-690">Ошибка = 4</span><span class="sxs-lookup"><span data-stu-id="e8b21-690">Error = 4</span></span>

  <span data-ttu-id="e8b21-691">Для ошибок и исключений, которые не могут быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="e8b21-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="e8b21-692">Эти сообщения указывают на сбой текущего действия или операции (например, текущего HTTP-запроса), а не на ошибку уровня приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="e8b21-693">Пример сообщения журнала: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="e8b21-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="e8b21-694">Критический = 5</span><span class="sxs-lookup"><span data-stu-id="e8b21-694">Critical = 5</span></span>

  <span data-ttu-id="e8b21-695">Для сбоев, которые требуют немедленного внимания.</span><span class="sxs-lookup"><span data-stu-id="e8b21-695">For failures that require immediate attention.</span></span> <span data-ttu-id="e8b21-696">Примеры: потеря данных, нехватка места на диске.</span><span class="sxs-lookup"><span data-stu-id="e8b21-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="e8b21-697">Уровень ведения журналов можно использовать для управления объемом выходных данных, записываемых на определенный носитель или в окно отображения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="e8b21-698">Пример:</span><span class="sxs-lookup"><span data-stu-id="e8b21-698">For example:</span></span>

* <span data-ttu-id="e8b21-699">В рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="e8b21-699">In production:</span></span>
  * <span data-ttu-id="e8b21-700">При ведении журнала на уровнях с `Trace` по `Information` создается большой объем подробных сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="e8b21-701">Чтобы контролировать затраты и не превысить лимиты объема хранилища данных, записывайте сообщения на уровнях с `Trace` по `Information` в хранилище данных с низкими затратами и большим объемом.</span><span class="sxs-lookup"><span data-stu-id="e8b21-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="e8b21-702">Ведение журнала на уровнях с `Warning` по `Critical` обычно приводит к записи меньшего количества сообщений меньшего размера.</span><span class="sxs-lookup"><span data-stu-id="e8b21-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="e8b21-703">Следовательно, затраты и лимиты хранилища не становятся проблемой, что приводит к большей гибкости при выборе хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="e8b21-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="e8b21-704">Во время разработки:</span><span class="sxs-lookup"><span data-stu-id="e8b21-704">During development:</span></span>
  * <span data-ttu-id="e8b21-705">Записывайте сообщения уровней с `Warning` по `Critical` на консоль.</span><span class="sxs-lookup"><span data-stu-id="e8b21-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="e8b21-706">Добавляйте сообщения уровней с `Trace` по `Information` при устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="e8b21-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="e8b21-707">В разделе [Фильтрация журналов](#log-filtering) далее в этой статье приводятся сведения о том, как контролировать уровни журнала, обрабатываемые поставщиком.</span><span class="sxs-lookup"><span data-stu-id="e8b21-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="e8b21-708">ASP.NET Core создает журналы для событий платформы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="e8b21-709">В примерах журнала ранее в этой статье не указывались журналы с уровнем ниже `Information`, поэтому журналы уровня `Debug` или `Trace` не создавались.</span><span class="sxs-lookup"><span data-stu-id="e8b21-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="e8b21-710">Ниже приведен пример журналов консоли, которые созданы при запуске примера приложения, настроенного на отображение журналов `Debug`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="e8b21-711">Идентификатор события журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-711">Log event ID</span></span>

<span data-ttu-id="e8b21-712">Каждый журнал может указывать *идентификатор события*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="e8b21-713">В примере приложения для этого используется локально определенный класс `LoggingEvents`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="e8b21-714">Идентификатор события связывает набор событий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-714">An event ID associates a set of events.</span></span> <span data-ttu-id="e8b21-715">Например, все журналы, связанные с отображением списка элементов на странице, могут иметь значение 1001.</span><span class="sxs-lookup"><span data-stu-id="e8b21-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="e8b21-716">Поставщик ведения журналов может хранить идентификатор события в поле идентификатора, в сообщении журнала, или вообще не хранить.</span><span class="sxs-lookup"><span data-stu-id="e8b21-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="e8b21-717">Поставщик Debug не отображает идентификаторы событий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="e8b21-718">Поставщик Console отображает идентификаторы событий в квадратных скобках после категории:</span><span class="sxs-lookup"><span data-stu-id="e8b21-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="e8b21-719">Шаблон сообщения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-719">Log message template</span></span>

<span data-ttu-id="e8b21-720">Каждый журнал указывает шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-720">Each log specifies a message template.</span></span> <span data-ttu-id="e8b21-721">Шаблон сообщения может содержать заполнители, для которых предоставляются аргументы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="e8b21-722">Используйте для заполнителей имена, а не числа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="e8b21-723">Параметры, используемые для предоставления значений, определяются порядком заполнителей, а не их именами.</span><span class="sxs-lookup"><span data-stu-id="e8b21-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="e8b21-724">В приведенном ниже коде обратите внимание на то, что имена параметров идут не по порядку в шаблоне сообщения:</span><span class="sxs-lookup"><span data-stu-id="e8b21-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="e8b21-725">Этот код создает сообщение журнала со значениями параметров в определенном порядке:</span><span class="sxs-lookup"><span data-stu-id="e8b21-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="e8b21-726">Платформа ведения журналов поддерживает такое поведение, чтобы поставщики ведения журнала могли реализовывать [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="e8b21-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="e8b21-727">Сами аргументы передаются в систему ведения журналов, а не только в отформатированный шаблон сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="e8b21-728">Эта информация позволяет поставщикам ведения журналов хранить значения параметров как поля.</span><span class="sxs-lookup"><span data-stu-id="e8b21-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="e8b21-729">Предположим, например, что вызовы метода средства ведения журналов выглядят так:</span><span class="sxs-lookup"><span data-stu-id="e8b21-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="e8b21-730">Если вы отправляете журналы в Хранилище таблиц Azure, каждая сущность таблицы Azure может иметь свойства `ID` и `RequestTime`, что упрощает выполнение запросов к данным журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="e8b21-731">Запрос может находить все журналы в пределах определенного диапазона `RequestTime`, не анализируя время ожидания текстового сообщения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="e8b21-732">Ведение журнала исключений</span><span class="sxs-lookup"><span data-stu-id="e8b21-732">Logging exceptions</span></span>

<span data-ttu-id="e8b21-733">Методы средства ведения журнала имеют перегрузки, которые позволяют передавать исключение, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e8b21-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="e8b21-734">Разные поставщики обрабатывают сведения об исключениях по-разному.</span><span class="sxs-lookup"><span data-stu-id="e8b21-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="e8b21-735">Ниже приведен пример выходных данных поставщика Debug из приведенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="e8b21-736">Фильтрация журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-736">Log filtering</span></span>

<span data-ttu-id="e8b21-737">Можно указать минимальный уровень ведения журнала для определенного поставщика и категории или для всех поставщиков или всех категорий.</span><span class="sxs-lookup"><span data-stu-id="e8b21-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="e8b21-738">Все журналы с уровнем ниже минимального не передаются этому поставщику, поэтому они не отображаются и не сохраняются.</span><span class="sxs-lookup"><span data-stu-id="e8b21-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="e8b21-739">Чтобы проигнорировать все журналы, укажите `LogLevel.None` в качестве минимального уровня ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="e8b21-740">`LogLevel.None` равно целочисленному значению 6, то есть больше, чем `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="e8b21-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="e8b21-741">Создание правил фильтрации в конфигурации</span><span class="sxs-lookup"><span data-stu-id="e8b21-741">Create filter rules in configuration</span></span>

<span data-ttu-id="e8b21-742">Код шаблона проектов вызывает `CreateDefaultBuilder` для настройки ведения журналов для поставщиков Console, Debug и EventSource (ASP.NET Core 2.2 или более поздняя версия).</span><span class="sxs-lookup"><span data-stu-id="e8b21-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="e8b21-743">Метод `CreateDefaultBuilder` настраивает ведение журнала для просмотра конфигурации в разделе `Logging`, как было описано [ранее в этой статье](#configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="e8b21-744">Данные конфигурации указывают минимальные уровни ведения журнала для каждого поставщика и категории, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e8b21-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="e8b21-745">Этот JSON создает шесть правил фильтрации: одно для поставщика Debug, четыре для поставщика Console и одно для всех поставщиков.</span><span class="sxs-lookup"><span data-stu-id="e8b21-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="e8b21-746">При создании объекта `ILogger` для каждого поставщика выбирается только одно из этих правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="e8b21-747">Правила фильтрации в коде</span><span class="sxs-lookup"><span data-stu-id="e8b21-747">Filter rules in code</span></span>

<span data-ttu-id="e8b21-748">В следующем примере показано, как зарегистрировать в коде правила фильтрации:</span><span class="sxs-lookup"><span data-stu-id="e8b21-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="e8b21-749">Второй `AddFilter` указывает поставщика, Debug, используя имя его типа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="e8b21-750">Первый `AddFilter` применяется ко всем поставщикам, так как он не определяет тип поставщика.</span><span class="sxs-lookup"><span data-stu-id="e8b21-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="e8b21-751">Применение правил фильтрации</span><span class="sxs-lookup"><span data-stu-id="e8b21-751">How filtering rules are applied</span></span>

<span data-ttu-id="e8b21-752">Данные конфигурации и код `AddFilter`, приведенный в предыдущих примерах, создают правила, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="e8b21-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="e8b21-753">Первые шесть взяты из примера конфигурации, а последние два — из примера кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="e8b21-754">Число</span><span class="sxs-lookup"><span data-stu-id="e8b21-754">Number</span></span> | <span data-ttu-id="e8b21-755">Поставщик</span><span class="sxs-lookup"><span data-stu-id="e8b21-755">Provider</span></span>      | <span data-ttu-id="e8b21-756">Категории, которые начинаются с...</span><span class="sxs-lookup"><span data-stu-id="e8b21-756">Categories that begin with ...</span></span>          | <span data-ttu-id="e8b21-757">Минимальный уровень ведения журнала</span><span class="sxs-lookup"><span data-stu-id="e8b21-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="e8b21-758">1</span><span class="sxs-lookup"><span data-stu-id="e8b21-758">1</span></span>      | <span data-ttu-id="e8b21-759">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-759">Debug</span></span>         | <span data-ttu-id="e8b21-760">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-760">All categories</span></span>                          | <span data-ttu-id="e8b21-761">Сведения</span><span class="sxs-lookup"><span data-stu-id="e8b21-761">Information</span></span>       |
| <span data-ttu-id="e8b21-762">2</span><span class="sxs-lookup"><span data-stu-id="e8b21-762">2</span></span>      | <span data-ttu-id="e8b21-763">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-763">Console</span></span>       | <span data-ttu-id="e8b21-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="e8b21-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="e8b21-765">Предупреждение</span><span class="sxs-lookup"><span data-stu-id="e8b21-765">Warning</span></span>           |
| <span data-ttu-id="e8b21-766">3</span><span class="sxs-lookup"><span data-stu-id="e8b21-766">3</span></span>      | <span data-ttu-id="e8b21-767">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-767">Console</span></span>       | <span data-ttu-id="e8b21-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="e8b21-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="e8b21-769">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-769">Debug</span></span>             |
| <span data-ttu-id="e8b21-770">4</span><span class="sxs-lookup"><span data-stu-id="e8b21-770">4</span></span>      | <span data-ttu-id="e8b21-771">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-771">Console</span></span>       | <span data-ttu-id="e8b21-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="e8b21-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="e8b21-773">Ошибка</span><span class="sxs-lookup"><span data-stu-id="e8b21-773">Error</span></span>             |
| <span data-ttu-id="e8b21-774">5</span><span class="sxs-lookup"><span data-stu-id="e8b21-774">5</span></span>      | <span data-ttu-id="e8b21-775">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-775">Console</span></span>       | <span data-ttu-id="e8b21-776">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-776">All categories</span></span>                          | <span data-ttu-id="e8b21-777">Сведения</span><span class="sxs-lookup"><span data-stu-id="e8b21-777">Information</span></span>       |
| <span data-ttu-id="e8b21-778">6</span><span class="sxs-lookup"><span data-stu-id="e8b21-778">6</span></span>      | <span data-ttu-id="e8b21-779">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="e8b21-779">All providers</span></span> | <span data-ttu-id="e8b21-780">Все категории</span><span class="sxs-lookup"><span data-stu-id="e8b21-780">All categories</span></span>                          | <span data-ttu-id="e8b21-781">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-781">Debug</span></span>             |
| <span data-ttu-id="e8b21-782">7</span><span class="sxs-lookup"><span data-stu-id="e8b21-782">7</span></span>      | <span data-ttu-id="e8b21-783">Все поставщики</span><span class="sxs-lookup"><span data-stu-id="e8b21-783">All providers</span></span> | <span data-ttu-id="e8b21-784">Система</span><span class="sxs-lookup"><span data-stu-id="e8b21-784">System</span></span>                                  | <span data-ttu-id="e8b21-785">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-785">Debug</span></span>             |
| <span data-ttu-id="e8b21-786">8</span><span class="sxs-lookup"><span data-stu-id="e8b21-786">8</span></span>      | <span data-ttu-id="e8b21-787">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-787">Debug</span></span>         | <span data-ttu-id="e8b21-788">Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e8b21-788">Microsoft</span></span>                               | <span data-ttu-id="e8b21-789">Трассировка</span><span class="sxs-lookup"><span data-stu-id="e8b21-789">Trace</span></span>             |

<span data-ttu-id="e8b21-790">При создании объекта `ILogger` объект `ILoggerFactory` выбирает одно правило для каждого поставщика, которое будет применено к этому средству ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="e8b21-791">Все сообщения, записываемые с помощью экземпляра `ILogger`, фильтруются на основе выбранных правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="e8b21-792">Самое подходящее правило для каждой пары поставщика и категории выбирается из списка доступных правил.</span><span class="sxs-lookup"><span data-stu-id="e8b21-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="e8b21-793">При создании `ILogger` для данной категории для каждого поставщика используется приведенный далее алгоритм:</span><span class="sxs-lookup"><span data-stu-id="e8b21-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="e8b21-794">Выберите все правила, которые соответствуют поставщику или его псевдониму.</span><span class="sxs-lookup"><span data-stu-id="e8b21-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="e8b21-795">Если ничего не найдено, выберите все правила с пустым поставщиком.</span><span class="sxs-lookup"><span data-stu-id="e8b21-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="e8b21-796">В результатах предыдущего шага выберите правила с самым длинным соответствующим префиксом категории.</span><span class="sxs-lookup"><span data-stu-id="e8b21-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="e8b21-797">Если ничего не найдено, выберите все правила, которые не указывают категорию.</span><span class="sxs-lookup"><span data-stu-id="e8b21-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="e8b21-798">Если выбрано несколько правил, примите **последнее**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="e8b21-799">Если правила не выбраны, используйте `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="e8b21-800">Предположим, у вас есть указанный выше список правил и вы создаете объект `ILogger` для категории Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine:</span><span class="sxs-lookup"><span data-stu-id="e8b21-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="e8b21-801">К поставщику Debug применяются правила 1, 6 и 8.</span><span class="sxs-lookup"><span data-stu-id="e8b21-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="e8b21-802">Правило 8 является наиболее подходящим, поэтому выбрано оно.</span><span class="sxs-lookup"><span data-stu-id="e8b21-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="e8b21-803">К поставщику отладки применяются правила 3, 4, 5 и 6.</span><span class="sxs-lookup"><span data-stu-id="e8b21-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="e8b21-804">Правило 3 является наиболее подходящим.</span><span class="sxs-lookup"><span data-stu-id="e8b21-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="e8b21-805">Полученный в результате экземпляр `ILogger` отправляет журналы уровня `Trace` и выше в поставщик Debug.</span><span class="sxs-lookup"><span data-stu-id="e8b21-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="e8b21-806">Журналы уровня `Debug` и выше отправляются в поставщик Console.</span><span class="sxs-lookup"><span data-stu-id="e8b21-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="e8b21-807">Псевдонимы поставщиков</span><span class="sxs-lookup"><span data-stu-id="e8b21-807">Provider aliases</span></span>

<span data-ttu-id="e8b21-808">Каждый поставщик определяет *псевдоним*, используемый в конфигурации вместо полного имени типа.</span><span class="sxs-lookup"><span data-stu-id="e8b21-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="e8b21-809">Для встроенных поставщиков используйте следующие псевдонимы:</span><span class="sxs-lookup"><span data-stu-id="e8b21-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="e8b21-810">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-810">Console</span></span>
* <span data-ttu-id="e8b21-811">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-811">Debug</span></span>
* <span data-ttu-id="e8b21-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-812">EventSource</span></span>
* <span data-ttu-id="e8b21-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-813">EventLog</span></span>
* <span data-ttu-id="e8b21-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-814">TraceSource</span></span>
* <span data-ttu-id="e8b21-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e8b21-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="e8b21-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e8b21-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="e8b21-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e8b21-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="e8b21-818">Минимальный уровень по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e8b21-818">Default minimum level</span></span>

<span data-ttu-id="e8b21-819">Существует параметр минимального уровня, который применяется, только если к определенному поставщику или категории не подходят правила из конфигурации или кода.</span><span class="sxs-lookup"><span data-stu-id="e8b21-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="e8b21-820">В следующем примере показано, как задать минимальный уровень:</span><span class="sxs-lookup"><span data-stu-id="e8b21-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="e8b21-821">Если минимальный уровень не задан явным образом, используется значение по умолчанию — `Information`, означающее, что журналы `Trace` и `Debug` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="e8b21-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="e8b21-822">Функции фильтрации</span><span class="sxs-lookup"><span data-stu-id="e8b21-822">Filter functions</span></span>

<span data-ttu-id="e8b21-823">Функция фильтрации вызывается для всех поставщиков и категорий, которым в конфигурации и (или) коде не назначены правила.</span><span class="sxs-lookup"><span data-stu-id="e8b21-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="e8b21-824">Код в функции имеет доступ к типу поставщика, категории и уровню ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="e8b21-825">Пример:</span><span class="sxs-lookup"><span data-stu-id="e8b21-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="e8b21-826">Системные категории и уровни</span><span class="sxs-lookup"><span data-stu-id="e8b21-826">System categories and levels</span></span>

<span data-ttu-id="e8b21-827">Ниже приведены некоторые категории, используемые ASP.NET Core и Entity Framework Core с заметками о журналах:</span><span class="sxs-lookup"><span data-stu-id="e8b21-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="e8b21-828">Категория</span><span class="sxs-lookup"><span data-stu-id="e8b21-828">Category</span></span>                            | <span data-ttu-id="e8b21-829">Примечания</span><span class="sxs-lookup"><span data-stu-id="e8b21-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="e8b21-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="e8b21-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="e8b21-831">Общая диагностика ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="e8b21-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="e8b21-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="e8b21-833">Распознанные, найденные и использованные ключи.</span><span class="sxs-lookup"><span data-stu-id="e8b21-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="e8b21-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="e8b21-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="e8b21-835">Разрешенные узлы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-835">Hosts allowed.</span></span> |
| <span data-ttu-id="e8b21-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="e8b21-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="e8b21-837">Время начала и длительность выполнения HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="e8b21-838">Определение загруженных начальных сборок размещения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="e8b21-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="e8b21-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="e8b21-840">Диагностика MVC и Razor.</span><span class="sxs-lookup"><span data-stu-id="e8b21-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="e8b21-841">Привязка моделей, выполнение фильтра, компиляция представлений, выбор действия.</span><span class="sxs-lookup"><span data-stu-id="e8b21-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="e8b21-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="e8b21-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="e8b21-843">Перенаправление соответствующих сведений.</span><span class="sxs-lookup"><span data-stu-id="e8b21-843">Route matching information.</span></span> |
| <span data-ttu-id="e8b21-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="e8b21-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="e8b21-845">Запуск, остановка и сохранение ответов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="e8b21-846">Сведения о сертификате HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e8b21-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="e8b21-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="e8b21-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="e8b21-848">Обработанные файлы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-848">Files served.</span></span> |
| <span data-ttu-id="e8b21-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="e8b21-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="e8b21-850">Общая диагностика Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="e8b21-851">Действия и конфигурация базы данных, обнаружение изменений, переносы.</span><span class="sxs-lookup"><span data-stu-id="e8b21-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="e8b21-852">Области журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-852">Log scopes</span></span>

 <span data-ttu-id="e8b21-853">*Область* может группировать набор логических операций.</span><span class="sxs-lookup"><span data-stu-id="e8b21-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="e8b21-854">Эту возможность можно использовать для присоединения одних и тех же данных к каждому журналу, созданному как часть набора.</span><span class="sxs-lookup"><span data-stu-id="e8b21-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="e8b21-855">Например, каждый журнал, созданный в ходе обработки транзакции, может включать идентификатор транзакции.</span><span class="sxs-lookup"><span data-stu-id="e8b21-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="e8b21-856">Область — это тип `IDisposable`, возвращаемый методом <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> и действующий до удаления.</span><span class="sxs-lookup"><span data-stu-id="e8b21-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="e8b21-857">Используйте область, заключив вызовы средства ведения журналов в блок `using`:</span><span class="sxs-lookup"><span data-stu-id="e8b21-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="e8b21-858">Следующий код предоставляет области для поставщика Console:</span><span class="sxs-lookup"><span data-stu-id="e8b21-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="e8b21-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="e8b21-860">Для включения ведения журнала на уровне области требуется параметр `IncludeScopes` средства ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="e8b21-861">Сведения о настройках см. в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="e8b21-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="e8b21-862">Каждое сообщение журнала содержит ограниченную информацию:</span><span class="sxs-lookup"><span data-stu-id="e8b21-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="e8b21-863">Встроенные поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-863">Built-in logging providers</span></span>

<span data-ttu-id="e8b21-864">В состав ASP.NET Core входят следующие поставщики:</span><span class="sxs-lookup"><span data-stu-id="e8b21-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="e8b21-865">Консоль</span><span class="sxs-lookup"><span data-stu-id="e8b21-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="e8b21-866">Отладка</span><span class="sxs-lookup"><span data-stu-id="e8b21-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="e8b21-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="e8b21-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="e8b21-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="e8b21-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e8b21-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e8b21-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e8b21-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e8b21-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e8b21-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="e8b21-873">Сведения о stdout и ведении журнала отладки с помощью модуля ASP.NET Core см. в статьях <xref:test/troubleshoot-azure-iis> и <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="e8b21-874">Поставщик Console</span><span class="sxs-lookup"><span data-stu-id="e8b21-874">Console provider</span></span>

<span data-ttu-id="e8b21-875">Пакет поставщика [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) отправляет выходные данные журнала на консоль.</span><span class="sxs-lookup"><span data-stu-id="e8b21-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="e8b21-876">Чтобы просмотреть выходные данные ведения журналов в консоли, откройте командную строку, перейдите в папку проекта и запустите приведенную ниже команду:</span><span class="sxs-lookup"><span data-stu-id="e8b21-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="e8b21-877">Поставщик Debug</span><span class="sxs-lookup"><span data-stu-id="e8b21-877">Debug provider</span></span>

<span data-ttu-id="e8b21-878">Пакет поставщика [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) записывает выходные данные журналов с помощью класса [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (вызовов метода `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="e8b21-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="e8b21-879">В Linux этот поставщик записывает журналы в каталог */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="e8b21-880">Поставщик источника событий</span><span class="sxs-lookup"><span data-stu-id="e8b21-880">Event Source provider</span></span>

<span data-ttu-id="e8b21-881">Пакет поставщика [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) записывает данные в источник событий на кросс-платформенный сервер с именем `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="e8b21-882">В Windows поставщик использует [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="e8b21-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="e8b21-883">Поставщик источника событий добавляется автоматически при вызове `CreateDefaultBuilder` для сборки узла.</span><span class="sxs-lookup"><span data-stu-id="e8b21-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="e8b21-884">Для сбора и просмотра данных журналов рекомендуется использовать [программу PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="e8b21-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="e8b21-885">Существуют и другие средства для просмотра журналов трассировки событий Windows, но PerfView обеспечивает максимальное удобство работы с событиями трассировки событий Windows, создаваемыми ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="e8b21-886">Чтобы настроить PerfView для сбора событий, регистрируемых этим поставщиком, добавьте строку `*Microsoft-Extensions-Logging` в список **Дополнительные поставщики**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="e8b21-887">(Не пропустите звездочку в начале строки.)</span><span class="sxs-lookup"><span data-stu-id="e8b21-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Дополнительные поставщики Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="e8b21-889">Поставщик Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="e8b21-889">Windows EventLog provider</span></span>

<span data-ttu-id="e8b21-890">Пакет поставщика [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) отправляет выходные данные журнала в журнал событий Windows.</span><span class="sxs-lookup"><span data-stu-id="e8b21-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="e8b21-891">[Перегрузки AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) позволяют передавать <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="e8b21-892">Если `null` или не указан, используются следующие параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="e8b21-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="e8b21-893">`LogName` &ndash; "Приложение"</span><span class="sxs-lookup"><span data-stu-id="e8b21-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="e8b21-894">`SourceName` &ndash; "Среда выполнения .NET"</span><span class="sxs-lookup"><span data-stu-id="e8b21-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="e8b21-895">`MachineName` &ndash; (локальный компьютер)</span><span class="sxs-lookup"><span data-stu-id="e8b21-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="e8b21-896">События регистрируются для [уровня предупреждения и более высоких уровней](#log-level).</span><span class="sxs-lookup"><span data-stu-id="e8b21-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="e8b21-897">Чтобы регистрировать события с уровнем ниже `Warning`, следует явно задать уровень ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="e8b21-898">Например, добавьте следующий код в файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e8b21-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="e8b21-899">Поставщик TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8b21-899">TraceSource provider</span></span>

<span data-ttu-id="e8b21-900">Пакет поставщика [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) использует библиотеки и поставщики <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="e8b21-901">[Перегрузки AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) позволяют передавать исходный параметр и прослушиватель трассировки.</span><span class="sxs-lookup"><span data-stu-id="e8b21-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="e8b21-902">Чтобы использовать этот поставщик, приложение должно выполняться в .NET Framework (а не в .NET Core).</span><span class="sxs-lookup"><span data-stu-id="e8b21-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="e8b21-903">Поставщик позволяет перенаправлять сообщения различным [прослушивателям](/dotnet/framework/debug-trace-profile/trace-listeners), например <xref:System.Diagnostics.TextWriterTraceListener>, который используется в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="e8b21-904">Поставщик службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-904">Azure App Service provider</span></span>

<span data-ttu-id="e8b21-905">Пакет поставщика [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) записывает журналы в текстовые файлы в файловой системе приложения службы приложений Azure и в [хранилище больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) в учетной записи хранения Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="e8b21-906">Этот пакет не входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e8b21-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="e8b21-907">Если планируется использовать .NET Framework или будет указана ссылка на метапакет `Microsoft.AspNetCore.App`, добавьте пакет поставщика в проект.</span><span class="sxs-lookup"><span data-stu-id="e8b21-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="e8b21-908">Перегрузка <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> позволяет передавать <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="e8b21-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="e8b21-909">Объект параметров может переопределять параметры по умолчанию, например шаблон выходных данных ведения журналов, имя BLOB-объекта и максимально допустимый размер файла.</span><span class="sxs-lookup"><span data-stu-id="e8b21-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="e8b21-910">(*Шаблон выходных данных* — это шаблон сообщений, который применяется ко всем журналам наряду с тем, что предоставляется при вызове метода `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="e8b21-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="e8b21-911">При развертывании в приложение Службы приложений ваше приложение использует параметры в разделе [Журналы Службы приложений](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) на странице **Служба приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="e8b21-912">При обновлении следующих параметров изменения вступают в силу немедленно без перезапуска или повторного развертывания приложения:</span><span class="sxs-lookup"><span data-stu-id="e8b21-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="e8b21-913">**Ведение журнала приложения (файловая система)** ;</span><span class="sxs-lookup"><span data-stu-id="e8b21-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="e8b21-914">**Ведение журнала приложения (BLOB-объект)** .</span><span class="sxs-lookup"><span data-stu-id="e8b21-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="e8b21-915">По умолчанию файлы журнала находятся в папке *D:\\home\\LogFiles\\Application*, а имя файла по умолчанию — *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="e8b21-916">Максимальный размер файла по умолчанию составляет 10 МБ, а максимальное количество сохраняемых по умолчанию файлов равно 2.</span><span class="sxs-lookup"><span data-stu-id="e8b21-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="e8b21-917">Имя BLOB-объекта по умолчанию — *{имя_приложения}{метка_времени}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="e8b21-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="e8b21-918">Поставщик работает только при выполнении проекта в среде Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b21-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="e8b21-919">Он не функционирует при запуске проекта в локальной среде &mdash;(то есть не выполняет запись в локальные файлы или в локальное хранилище разработки для больших двоичных объектов).</span><span class="sxs-lookup"><span data-stu-id="e8b21-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="e8b21-920">Потоковая передача журналов Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-920">Azure log streaming</span></span>

<span data-ttu-id="e8b21-921">Потоковая передача журналов Azure позволяет просматривать активность журнала в реальном времени из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="e8b21-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="e8b21-922">сервер приложений;</span><span class="sxs-lookup"><span data-stu-id="e8b21-922">The app server</span></span>
* <span data-ttu-id="e8b21-923">веб-сервер;</span><span class="sxs-lookup"><span data-stu-id="e8b21-923">The web server</span></span>
* <span data-ttu-id="e8b21-924">трассировка неудачно завершенных запросов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-924">Failed request tracing</span></span>

<span data-ttu-id="e8b21-925">Настройка потоковой передачи журналов Azure</span><span class="sxs-lookup"><span data-stu-id="e8b21-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="e8b21-926">Со страницы портала приложения перейдите на страницу **Журналы Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="e8b21-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="e8b21-927">**Включите** параметр **Ведение журнала приложения (файловая система)** .</span><span class="sxs-lookup"><span data-stu-id="e8b21-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="e8b21-928">Выберите **уровень** ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e8b21-928">Choose the log **Level**.</span></span> <span data-ttu-id="e8b21-929">Этот параметр применяется только к потоковой передаче журналов Azure, а не к другим поставщикам ведения журнала в приложении.</span><span class="sxs-lookup"><span data-stu-id="e8b21-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="e8b21-930">Перейдите на страницу **Поток журналов**, чтобы просмотреть сообщения приложения.</span><span class="sxs-lookup"><span data-stu-id="e8b21-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="e8b21-931">Они записываются в журнал приложением через интерфейс `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e8b21-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="e8b21-932">Ведение журнала трассировки Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="e8b21-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="e8b21-933">Пакет поставщика [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) записывает журналы в Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="e8b21-934">Служба Application Insights отслеживает веб-приложения и предоставляет средства для создания запросов и анализа данных телеметрии.</span><span class="sxs-lookup"><span data-stu-id="e8b21-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="e8b21-935">Используя этого поставщика, вы сможете выполнять запросы к журналам и их анализ с помощью средств Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e8b21-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="e8b21-936">Поставщик ведения журнала включается как зависимость [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Этот пакет предоставляет всю доступную телеметрию для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e8b21-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="e8b21-937">Если вы используете этот пакет, пакет поставщика устанавливать не нужно.</span><span class="sxs-lookup"><span data-stu-id="e8b21-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="e8b21-938">Не используйте пакет [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web), который предназначен для ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="e8b21-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="e8b21-939">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="e8b21-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e8b21-940">Общие сведения об Application Insights</span><span class="sxs-lookup"><span data-stu-id="e8b21-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="e8b21-941">[Application Insights для приложений ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — начните изучение с этой статьи, если вы хотите полностью реализовать возможности Application Insights для телеметрии и ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="e8b21-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) (Поставщик ведения журналов Application Insights для журналов .NET Core ILogger) — начните изучение с этой статьи, если вы хотите внедрить поставщика ведения журналов, не используя остальные возможности Application Insights для телеметрии.</span><span class="sxs-lookup"><span data-stu-id="e8b21-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="e8b21-943">[Адаптеры ведения журналов в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="e8b21-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="e8b21-944">[Инструментирование серверного кода веб-приложения с помощью Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — интерактивный учебник на сайте Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="e8b21-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="e8b21-945">Сторонние поставщики ведения журналов</span><span class="sxs-lookup"><span data-stu-id="e8b21-945">Third-party logging providers</span></span>

<span data-ttu-id="e8b21-946">Некоторые сторонние платформы ведения журналов, которые работают с ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e8b21-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="e8b21-947">[ELMAH.IO](https://elmah.io/) ([в репозитории GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([в репозитории GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="e8b21-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="e8b21-949">[JSNLog](https://jsnlog.com/) ([в репозитории GitHub](https://github.com/mperdeck/jsnlog));</span><span class="sxs-lookup"><span data-stu-id="e8b21-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="e8b21-950">[KissLog.net](https://kisslog.net/) ([репозиторий GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="e8b21-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="e8b21-951">[Log4Net](https://logging.apache.org/log4net/) ([репозиторий GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="e8b21-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="e8b21-952">[Loggr](https://loggr.net/) ([в репозитории GitHub](https://github.com/imobile3/Loggr.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-953">[NLog](https://nlog-project.org/) ([в репозитории GitHub](https://github.com/NLog/NLog.Extensions.Logging));</span><span class="sxs-lookup"><span data-stu-id="e8b21-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="e8b21-954">[Sentry](https://sentry.io/welcome/) ([репозиторий GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e8b21-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="e8b21-955">[Serilog](https://serilog.net/) ([в репозитории GitHub](https://github.com/serilog/serilog-aspnetcore)).</span><span class="sxs-lookup"><span data-stu-id="e8b21-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="e8b21-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([репозиторий Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e8b21-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="e8b21-957">Некоторые сторонние платформы выполняют [семантическое ведение журналов, также известное как структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="e8b21-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="e8b21-958">Использование сторонней платформы аналогично использованию одного из встроенных поставщиков:</span><span class="sxs-lookup"><span data-stu-id="e8b21-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="e8b21-959">Добавьте пакет NuGet в проект.</span><span class="sxs-lookup"><span data-stu-id="e8b21-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="e8b21-960">Вызовите метод расширения `ILoggerFactory`, предоставляемый платформой ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="e8b21-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="e8b21-961">Дополнительные сведения см. в документации по каждому поставщику.</span><span class="sxs-lookup"><span data-stu-id="e8b21-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="e8b21-962">Сторонние поставщики ведения журналов не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="e8b21-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8b21-963">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e8b21-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
