---
title: Универсальный узел .NET в ASP.NET Core
author: rick-anderson
description: Используйте универсальный узел .NET в приложениях ASP.NET Core.  Универсальный узел отвечает за запуск приложения и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762325"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="53f33-104">Универсальный узел .NET в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53f33-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="53f33-105">Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="53f33-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="53f33-106">В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f33-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="53f33-107">Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="53f33-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="53f33-108">Определение узла</span><span class="sxs-lookup"><span data-stu-id="53f33-108">Host definition</span></span>

<span data-ttu-id="53f33-109">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="53f33-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="53f33-110">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="53f33-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="53f33-111">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="53f33-111">Logging</span></span>
* <span data-ttu-id="53f33-112">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="53f33-112">Configuration</span></span>
* <span data-ttu-id="53f33-113">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="53f33-113">`IHostedService` implementations</span></span>

<span data-ttu-id="53f33-114">После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы.</span><span class="sxs-lookup"><span data-stu-id="53f33-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="53f33-115">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="53f33-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="53f33-116">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="53f33-117">Создание узла</span><span class="sxs-lookup"><span data-stu-id="53f33-117">Set up a host</span></span>

<span data-ttu-id="53f33-118">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="53f33-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="53f33-119">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="53f33-119">The `Main` method:</span></span>

* <span data-ttu-id="53f33-120">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="53f33-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="53f33-121">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="53f33-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="53f33-122">Веб-шаблоны ASP.NET Core создают следующий код для создания узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="53f33-123">Следующий код создают нагрузку, отличную от HTTP, с одной реализацией `IHostedService`, добавленной в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="53f33-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="53f33-124">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="53f33-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="53f33-125">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="53f33-126">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="53f33-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="53f33-127">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="53f33-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="53f33-128">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="53f33-128">Default builder settings</span></span>

<span data-ttu-id="53f33-129">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="53f33-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="53f33-130">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="53f33-131">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="53f33-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="53f33-132">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="53f33-133">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="53f33-133">Command-line arguments.</span></span>
* <span data-ttu-id="53f33-134">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="53f33-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="53f33-135">*appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="53f33-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="53f33-136">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="53f33-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="53f33-137">[диспетчер секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="53f33-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="53f33-138">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-138">Environment variables.</span></span>
  * <span data-ttu-id="53f33-139">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="53f33-139">Command-line arguments.</span></span>
* <span data-ttu-id="53f33-140">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="53f33-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="53f33-141">Консоль</span><span class="sxs-lookup"><span data-stu-id="53f33-141">Console</span></span>
  * <span data-ttu-id="53f33-142">Отладка</span><span class="sxs-lookup"><span data-stu-id="53f33-142">Debug</span></span>
  * <span data-ttu-id="53f33-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="53f33-143">EventSource</span></span>
  * <span data-ttu-id="53f33-144">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="53f33-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="53f33-145">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="53f33-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="53f33-146">Метод `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="53f33-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="53f33-147">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="53f33-148">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="53f33-149">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="53f33-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="53f33-150">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="53f33-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="53f33-151">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="53f33-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="53f33-152">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="53f33-152">Enables IIS integration.</span></span> <span data-ttu-id="53f33-153">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="53f33-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="53f33-154">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="53f33-155">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="53f33-155">Framework-provided services</span></span>

<span data-ttu-id="53f33-156">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="53f33-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="53f33-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="53f33-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="53f33-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="53f33-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="53f33-160">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="53f33-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="53f33-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="53f33-162">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="53f33-163">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="53f33-164">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="53f33-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="53f33-165">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="53f33-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="53f33-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-166">IHostLifetime</span></span>

<span data-ttu-id="53f33-167">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="53f33-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="53f33-168">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="53f33-168">The last implementation registered is used.</span></span>

<span data-ttu-id="53f33-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="53f33-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="53f33-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="53f33-171">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="53f33-172">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="53f33-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="53f33-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="53f33-173">IHostEnvironment</span></span>

<span data-ttu-id="53f33-174">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="53f33-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="53f33-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="53f33-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="53f33-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="53f33-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="53f33-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="53f33-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="53f33-178">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="53f33-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="53f33-179">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="53f33-179">Host configuration</span></span>

<span data-ttu-id="53f33-180">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="53f33-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="53f33-181">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="53f33-182">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="53f33-183">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="53f33-184">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-185">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="53f33-186">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="53f33-187">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="53f33-188">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="53f33-189">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="53f33-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="53f33-190">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="53f33-191">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="53f33-191">App configuration</span></span>

<span data-ttu-id="53f33-192">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="53f33-193">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-194">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="53f33-195">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="53f33-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="53f33-196">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="53f33-197">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="53f33-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="53f33-198">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="53f33-198">Settings for all app types</span></span>

<span data-ttu-id="53f33-199">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="53f33-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="53f33-200">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="53f33-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="53f33-201">ApplicationName</span></span>

<span data-ttu-id="53f33-202">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="53f33-203">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="53f33-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="53f33-204">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-204">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-205">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="53f33-206">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="53f33-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="53f33-207">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="53f33-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-208">ContentRoot</span></span>

<span data-ttu-id="53f33-209">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="53f33-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="53f33-210">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="53f33-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="53f33-211">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="53f33-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="53f33-212">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-212">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-213">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="53f33-214">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="53f33-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="53f33-215">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="53f33-216">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="53f33-216">For more information, see:</span></span>

* [<span data-ttu-id="53f33-217">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="53f33-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="53f33-218">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="53f33-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="53f33-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="53f33-219">EnvironmentName</span></span>

<span data-ttu-id="53f33-220">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="53f33-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="53f33-221">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="53f33-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="53f33-222">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="53f33-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="53f33-223">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="53f33-223">**Key**: `environment`</span></span>  
<span data-ttu-id="53f33-224">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-224">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-225">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="53f33-225">**Default**: `Production`</span></span>  
<span data-ttu-id="53f33-226">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="53f33-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="53f33-227">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="53f33-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="53f33-228">ShutdownTimeout</span></span>

<span data-ttu-id="53f33-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="53f33-230">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="53f33-230">The default value is five seconds.</span></span>  <span data-ttu-id="53f33-231">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="53f33-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="53f33-232">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="53f33-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="53f33-233">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="53f33-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="53f33-234">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="53f33-235">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="53f33-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="53f33-236">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="53f33-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="53f33-237">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="53f33-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="53f33-238">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="53f33-238">**Type**: `int`</span></span>  
<span data-ttu-id="53f33-239">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="53f33-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="53f33-240">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="53f33-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="53f33-241">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="53f33-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="53f33-242">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="53f33-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="53f33-243">Отключение перезагрузки конфигурации приложения при изменении</span><span class="sxs-lookup"><span data-stu-id="53f33-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="53f33-244">[По умолчанию](xref:fundamentals/configuration/index#default) при изменении файла выполняется перезагрузка *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="53f33-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="53f33-245">Чтобы отключить эту функцию перезагрузки в ASP.NET Core 5.0 или более поздней версии, присвойте ключу `hostBuilder:reloadConfigOnChange` значение `false`.</span><span class="sxs-lookup"><span data-stu-id="53f33-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="53f33-246">**Ключ**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="53f33-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="53f33-247">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-248">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="53f33-248">**Default**: `true`</span></span>  
<span data-ttu-id="53f33-249">**Аргумент командной строки**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="53f33-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="53f33-250">**Переменная среды**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="53f33-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="53f33-251">Двоеточие (`:`) не работает в качестве разделителя с иерархическими ключами переменных среды на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="53f33-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="53f33-252">Дополнительную информацию см. в разделе [Переменные среды](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="53f33-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="53f33-253">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="53f33-253">Settings for web apps</span></span>

<span data-ttu-id="53f33-254">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="53f33-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="53f33-255">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="53f33-256">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="53f33-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="53f33-257">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="53f33-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="53f33-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="53f33-258">CaptureStartupErrors</span></span>

<span data-ttu-id="53f33-259">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="53f33-260">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="53f33-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="53f33-261">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="53f33-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="53f33-262">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-263">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="53f33-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="53f33-264">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="53f33-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="53f33-265">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="53f33-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="53f33-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="53f33-266">DetailedErrors</span></span>

<span data-ttu-id="53f33-267">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="53f33-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="53f33-268">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="53f33-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="53f33-269">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-270">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="53f33-270">**Default**: `false`</span></span>  
<span data-ttu-id="53f33-271">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="53f33-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="53f33-272">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="53f33-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="53f33-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="53f33-274">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="53f33-275">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="53f33-276">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="53f33-277">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="53f33-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="53f33-278">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-278">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-279">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="53f33-279">**Default**: Empty string</span></span>  
<span data-ttu-id="53f33-280">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="53f33-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="53f33-281">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="53f33-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="53f33-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="53f33-283">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="53f33-284">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="53f33-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="53f33-285">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-285">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-286">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="53f33-286">**Default**: Empty string</span></span>  
<span data-ttu-id="53f33-287">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="53f33-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="53f33-288">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="53f33-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="53f33-289">HTTPS_Port</span></span>

<span data-ttu-id="53f33-290">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f33-290">The HTTPS redirect port.</span></span> <span data-ttu-id="53f33-291">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="53f33-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="53f33-292">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="53f33-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="53f33-293">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-293">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-294">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="53f33-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="53f33-295">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="53f33-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="53f33-296">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="53f33-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="53f33-297">PreferHostingUrls</span></span>

<span data-ttu-id="53f33-298">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="53f33-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="53f33-299">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="53f33-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="53f33-300">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-301">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="53f33-301">**Default**: `true`</span></span>  
<span data-ttu-id="53f33-302">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="53f33-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="53f33-303">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="53f33-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="53f33-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="53f33-304">PreventHostingStartup</span></span>

<span data-ttu-id="53f33-305">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="53f33-306">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="53f33-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="53f33-307">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="53f33-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="53f33-308">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-309">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="53f33-309">**Default**: `false`</span></span>  
<span data-ttu-id="53f33-310">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="53f33-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="53f33-311">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="53f33-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="53f33-312">StartupAssembly</span></span>

<span data-ttu-id="53f33-313">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="53f33-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="53f33-314">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="53f33-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="53f33-315">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-315">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-316">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="53f33-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="53f33-317">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="53f33-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="53f33-318">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="53f33-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="53f33-319">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="53f33-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="53f33-320">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="53f33-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="53f33-321">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="53f33-321">URLs</span></span>

<span data-ttu-id="53f33-322">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="53f33-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="53f33-323">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="53f33-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="53f33-324">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="53f33-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="53f33-325">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="53f33-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="53f33-326">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="53f33-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="53f33-327">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="53f33-327">**Key**: `urls`</span></span>  
<span data-ttu-id="53f33-328">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-328">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-329">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="53f33-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="53f33-330">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="53f33-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="53f33-331">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="53f33-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="53f33-332">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="53f33-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="53f33-333">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="53f33-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="53f33-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-334">WebRoot</span></span>

<span data-ttu-id="53f33-335">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="53f33-336">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="53f33-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="53f33-337">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="53f33-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="53f33-338">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-338">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-339">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="53f33-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="53f33-340">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="53f33-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="53f33-341">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="53f33-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="53f33-342">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="53f33-343">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="53f33-343">For more information, see:</span></span>

* [<span data-ttu-id="53f33-344">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="53f33-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="53f33-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="53f33-346">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="53f33-346">Manage the host lifetime</span></span>

<span data-ttu-id="53f33-347">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="53f33-348">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="53f33-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="53f33-349">Выполнить</span><span class="sxs-lookup"><span data-stu-id="53f33-349">Run</span></span>

<span data-ttu-id="53f33-350">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="53f33-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="53f33-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-351">RunAsync</span></span>

<span data-ttu-id="53f33-352">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="53f33-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-353">RunConsoleAsync</span></span>

<span data-ttu-id="53f33-354">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="53f33-355">Начало</span><span class="sxs-lookup"><span data-stu-id="53f33-355">Start</span></span>

<span data-ttu-id="53f33-356">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="53f33-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="53f33-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-357">StartAsync</span></span>

<span data-ttu-id="53f33-358">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="53f33-359">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="53f33-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="53f33-360">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="53f33-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="53f33-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-361">StopAsync</span></span>

<span data-ttu-id="53f33-362">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="53f33-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="53f33-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="53f33-363">WaitForShutdown</span></span>

<span data-ttu-id="53f33-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="53f33-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="53f33-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="53f33-366">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="53f33-367">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="53f33-367">External control</span></span>

<span data-ttu-id="53f33-368">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="53f33-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="53f33-369">Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="53f33-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="53f33-370">В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f33-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="53f33-371">Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="53f33-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="53f33-372">Определение узла</span><span class="sxs-lookup"><span data-stu-id="53f33-372">Host definition</span></span>

<span data-ttu-id="53f33-373">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="53f33-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="53f33-374">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="53f33-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="53f33-375">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="53f33-375">Logging</span></span>
* <span data-ttu-id="53f33-376">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="53f33-376">Configuration</span></span>
* <span data-ttu-id="53f33-377">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="53f33-377">`IHostedService` implementations</span></span>

<span data-ttu-id="53f33-378">После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы.</span><span class="sxs-lookup"><span data-stu-id="53f33-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="53f33-379">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="53f33-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="53f33-380">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="53f33-381">Создание узла</span><span class="sxs-lookup"><span data-stu-id="53f33-381">Set up a host</span></span>

<span data-ttu-id="53f33-382">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="53f33-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="53f33-383">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="53f33-383">The `Main` method:</span></span>

* <span data-ttu-id="53f33-384">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="53f33-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="53f33-385">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="53f33-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="53f33-386">Веб-шаблоны ASP.NET Core создают следующий код для создания универсального узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="53f33-387">Следующий код создает универсальный узел, используя рабочую нагрузку, отличную от HTTP.</span><span class="sxs-lookup"><span data-stu-id="53f33-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="53f33-388">Реализация `IHostedService` добавляется в контейнер DI:</span><span class="sxs-lookup"><span data-stu-id="53f33-388">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="53f33-389">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="53f33-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="53f33-390">Приведенный выше код создается шаблонами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f33-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="53f33-391">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="53f33-392">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="53f33-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="53f33-393">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="53f33-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="53f33-394">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="53f33-394">Default builder settings</span></span>

<span data-ttu-id="53f33-395">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="53f33-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="53f33-396">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span><span class="sxs-lookup"><span data-stu-id="53f33-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="53f33-397">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="53f33-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="53f33-398">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="53f33-399">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="53f33-399">Command-line arguments.</span></span>
* <span data-ttu-id="53f33-400">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="53f33-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="53f33-401">*appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="53f33-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="53f33-402">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="53f33-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="53f33-403">[диспетчер секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="53f33-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="53f33-404">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-404">Environment variables.</span></span>
  * <span data-ttu-id="53f33-405">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="53f33-405">Command-line arguments.</span></span>
* <span data-ttu-id="53f33-406">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="53f33-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="53f33-407">Консоль</span><span class="sxs-lookup"><span data-stu-id="53f33-407">Console</span></span>
  * <span data-ttu-id="53f33-408">Отладка</span><span class="sxs-lookup"><span data-stu-id="53f33-408">Debug</span></span>
  * <span data-ttu-id="53f33-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="53f33-409">EventSource</span></span>
  * <span data-ttu-id="53f33-410">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="53f33-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="53f33-411">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="53f33-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="53f33-412">Метод `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="53f33-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="53f33-413">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="53f33-414">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="53f33-415">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="53f33-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="53f33-416">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="53f33-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="53f33-417">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="53f33-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="53f33-418">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="53f33-418">Enables IIS integration.</span></span> <span data-ttu-id="53f33-419">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="53f33-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="53f33-420">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="53f33-421">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="53f33-421">Framework-provided services</span></span>

<span data-ttu-id="53f33-422">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="53f33-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="53f33-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="53f33-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="53f33-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="53f33-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="53f33-426">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="53f33-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="53f33-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="53f33-428">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="53f33-429">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="53f33-430">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="53f33-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="53f33-431">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="53f33-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="53f33-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-432">IHostLifetime</span></span>

<span data-ttu-id="53f33-433">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="53f33-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="53f33-434">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="53f33-434">The last implementation registered is used.</span></span>

<span data-ttu-id="53f33-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="53f33-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="53f33-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="53f33-437">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="53f33-438">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="53f33-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="53f33-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="53f33-439">IHostEnvironment</span></span>

<span data-ttu-id="53f33-440">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="53f33-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="53f33-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="53f33-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="53f33-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="53f33-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="53f33-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="53f33-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="53f33-444">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="53f33-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="53f33-445">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="53f33-445">Host configuration</span></span>

<span data-ttu-id="53f33-446">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="53f33-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="53f33-447">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="53f33-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="53f33-448">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="53f33-449">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="53f33-450">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-451">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="53f33-452">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="53f33-453">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="53f33-454">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="53f33-455">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="53f33-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="53f33-456">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="53f33-457">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="53f33-457">App configuration</span></span>

<span data-ttu-id="53f33-458">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="53f33-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="53f33-459">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-460">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="53f33-461">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="53f33-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="53f33-462">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="53f33-463">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="53f33-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="53f33-464">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="53f33-464">Settings for all app types</span></span>

<span data-ttu-id="53f33-465">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="53f33-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="53f33-466">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="53f33-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="53f33-467">ApplicationName</span></span>

<span data-ttu-id="53f33-468">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="53f33-469">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="53f33-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="53f33-470">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-470">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-471">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="53f33-472">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="53f33-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="53f33-473">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="53f33-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-474">ContentRoot</span></span>

<span data-ttu-id="53f33-475">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="53f33-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="53f33-476">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="53f33-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="53f33-477">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="53f33-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="53f33-478">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-478">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-479">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="53f33-480">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="53f33-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="53f33-481">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="53f33-482">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="53f33-482">For more information, see:</span></span>

* [<span data-ttu-id="53f33-483">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="53f33-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="53f33-484">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="53f33-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="53f33-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="53f33-485">EnvironmentName</span></span>

<span data-ttu-id="53f33-486">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="53f33-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="53f33-487">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="53f33-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="53f33-488">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="53f33-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="53f33-489">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="53f33-489">**Key**: `environment`</span></span>  
<span data-ttu-id="53f33-490">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-490">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-491">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="53f33-491">**Default**: `Production`</span></span>  
<span data-ttu-id="53f33-492">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="53f33-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="53f33-493">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="53f33-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="53f33-494">ShutdownTimeout</span></span>

<span data-ttu-id="53f33-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="53f33-496">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="53f33-496">The default value is five seconds.</span></span>  <span data-ttu-id="53f33-497">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="53f33-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="53f33-498">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="53f33-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="53f33-499">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="53f33-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="53f33-500">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="53f33-501">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="53f33-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="53f33-502">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="53f33-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="53f33-503">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="53f33-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="53f33-504">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="53f33-504">**Type**: `int`</span></span>  
<span data-ttu-id="53f33-505">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="53f33-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="53f33-506">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="53f33-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="53f33-507">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="53f33-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="53f33-508">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="53f33-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="53f33-509">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="53f33-509">Settings for web apps</span></span>

<span data-ttu-id="53f33-510">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="53f33-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="53f33-511">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="53f33-512">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="53f33-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="53f33-513">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="53f33-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="53f33-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="53f33-514">CaptureStartupErrors</span></span>

<span data-ttu-id="53f33-515">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="53f33-516">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="53f33-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="53f33-517">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="53f33-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="53f33-518">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-519">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="53f33-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="53f33-520">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="53f33-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="53f33-521">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="53f33-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="53f33-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="53f33-522">DetailedErrors</span></span>

<span data-ttu-id="53f33-523">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="53f33-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="53f33-524">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="53f33-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="53f33-525">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-526">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="53f33-526">**Default**: `false`</span></span>  
<span data-ttu-id="53f33-527">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="53f33-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="53f33-528">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="53f33-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="53f33-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="53f33-530">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="53f33-531">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="53f33-532">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="53f33-533">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="53f33-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="53f33-534">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-534">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-535">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="53f33-535">**Default**: Empty string</span></span>  
<span data-ttu-id="53f33-536">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="53f33-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="53f33-537">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="53f33-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="53f33-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="53f33-539">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="53f33-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="53f33-540">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="53f33-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="53f33-541">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-541">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-542">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="53f33-542">**Default**: Empty string</span></span>  
<span data-ttu-id="53f33-543">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="53f33-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="53f33-544">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="53f33-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="53f33-545">HTTPS_Port</span></span>

<span data-ttu-id="53f33-546">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f33-546">The HTTPS redirect port.</span></span> <span data-ttu-id="53f33-547">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="53f33-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="53f33-548">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="53f33-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="53f33-549">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-549">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-550">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="53f33-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="53f33-551">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="53f33-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="53f33-552">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="53f33-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="53f33-553">PreferHostingUrls</span></span>

<span data-ttu-id="53f33-554">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="53f33-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="53f33-555">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="53f33-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="53f33-556">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-557">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="53f33-557">**Default**: `true`</span></span>  
<span data-ttu-id="53f33-558">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="53f33-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="53f33-559">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="53f33-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="53f33-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="53f33-560">PreventHostingStartup</span></span>

<span data-ttu-id="53f33-561">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="53f33-562">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="53f33-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="53f33-563">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="53f33-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="53f33-564">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="53f33-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="53f33-565">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="53f33-565">**Default**: `false`</span></span>  
<span data-ttu-id="53f33-566">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="53f33-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="53f33-567">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="53f33-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="53f33-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="53f33-568">StartupAssembly</span></span>

<span data-ttu-id="53f33-569">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="53f33-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="53f33-570">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="53f33-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="53f33-571">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-571">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-572">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="53f33-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="53f33-573">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="53f33-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="53f33-574">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="53f33-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="53f33-575">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="53f33-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="53f33-576">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="53f33-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="53f33-577">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="53f33-577">URLs</span></span>

<span data-ttu-id="53f33-578">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="53f33-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="53f33-579">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="53f33-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="53f33-580">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="53f33-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="53f33-581">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="53f33-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="53f33-582">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="53f33-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="53f33-583">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="53f33-583">**Key**: `urls`</span></span>  
<span data-ttu-id="53f33-584">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-584">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-585">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="53f33-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="53f33-586">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="53f33-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="53f33-587">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="53f33-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="53f33-588">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="53f33-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="53f33-589">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="53f33-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="53f33-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-590">WebRoot</span></span>

<span data-ttu-id="53f33-591">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="53f33-592">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="53f33-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="53f33-593">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="53f33-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="53f33-594">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-594">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-595">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="53f33-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="53f33-596">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="53f33-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="53f33-597">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="53f33-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="53f33-598">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="53f33-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="53f33-599">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="53f33-599">For more information, see:</span></span>

* [<span data-ttu-id="53f33-600">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="53f33-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="53f33-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="53f33-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="53f33-602">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="53f33-602">Manage the host lifetime</span></span>

<span data-ttu-id="53f33-603">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="53f33-604">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="53f33-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="53f33-605">Выполнить</span><span class="sxs-lookup"><span data-stu-id="53f33-605">Run</span></span>

<span data-ttu-id="53f33-606">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="53f33-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="53f33-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-607">RunAsync</span></span>

<span data-ttu-id="53f33-608">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="53f33-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-609">RunConsoleAsync</span></span>

<span data-ttu-id="53f33-610">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="53f33-611">Начало</span><span class="sxs-lookup"><span data-stu-id="53f33-611">Start</span></span>

<span data-ttu-id="53f33-612">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="53f33-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="53f33-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-613">StartAsync</span></span>

<span data-ttu-id="53f33-614">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="53f33-615">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="53f33-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="53f33-616">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="53f33-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="53f33-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-617">StopAsync</span></span>

<span data-ttu-id="53f33-618">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="53f33-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="53f33-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="53f33-619">WaitForShutdown</span></span>

<span data-ttu-id="53f33-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="53f33-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="53f33-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="53f33-622">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="53f33-623">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="53f33-623">External control</span></span>

<span data-ttu-id="53f33-624">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="53f33-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53f33-625">Приложения ASP.NET Core настраивают и запускают узел.</span><span class="sxs-lookup"><span data-stu-id="53f33-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="53f33-626">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="53f33-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="53f33-627">В этой статье рассматривается универсальный узел ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), используемый для приложений, которые не обрабатывают запросы HTTP.</span><span class="sxs-lookup"><span data-stu-id="53f33-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="53f33-628">Универсальный узел предназначен для отделения конвейера HTTP от API веб-узла, чтобы можно было иметь больше сценариев узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="53f33-629">Обмен сообщениями, фоновые задачи и другие рабочие нагрузки, не связанные с HTTP, используют перекрестные возможности универсальных узлов, такие как конфигурация, внедрение зависимости (DI) и ведение журналов.</span><span class="sxs-lookup"><span data-stu-id="53f33-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="53f33-630">Универсальный узел является новой возможностью ASP.NET Core 2.1 и не подходит для сценариев с веб-размещением.</span><span class="sxs-lookup"><span data-stu-id="53f33-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="53f33-631">Сведения о сценариях с веб-размещением см. в статье о [веб-узле](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="53f33-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="53f33-632">Универсальный узел заменит веб-узел в будущих версиях. Он будет выступать основным узлом API для сценариев HTTP и "не HTTP".</span><span class="sxs-lookup"><span data-stu-id="53f33-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="53f33-633">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53f33-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="53f33-634">При выполнении примера приложения в [Visual Studio Code](https://code.visualstudio.com/) используйте *внешний или встроенный терминал*.</span><span class="sxs-lookup"><span data-stu-id="53f33-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="53f33-635">Не выполняйте пример в `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="53f33-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="53f33-636">Настройка консоли в Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="53f33-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="53f33-637">Откройте файл *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="53f33-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="53f33-638">В разделе конфигурации **.NET Core Launch (console)** найдите параметр **console**.</span><span class="sxs-lookup"><span data-stu-id="53f33-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="53f33-639">Измените значение на `externalTerminal` или `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="53f33-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="53f33-640">Вступление</span><span class="sxs-lookup"><span data-stu-id="53f33-640">Introduction</span></span>

<span data-ttu-id="53f33-641">Библиотека универсального узла находится в пространстве имен <xref:Microsoft.Extensions.Hosting> и включена в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="53f33-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="53f33-642">Пакет `Microsoft.Extensions.Hosting` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="53f33-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="53f33-643"><xref:Microsoft.Extensions.Hosting.IHostedService> — это точка входа для выполнения кода.</span><span class="sxs-lookup"><span data-stu-id="53f33-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="53f33-644">Каждая реализация <xref:Microsoft.Extensions.Hosting.IHostedService> выполняется в порядке [регистрации служб в ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="53f33-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="53f33-645">Метод <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> вызывается в каждом <xref:Microsoft.Extensions.Hosting.IHostedService> при запуске узла, а метод <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> вызывается в порядке, обратном регистрации, когда узел корректно завершает работу.</span><span class="sxs-lookup"><span data-stu-id="53f33-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="53f33-646">Создание узла</span><span class="sxs-lookup"><span data-stu-id="53f33-646">Set up a host</span></span>

<span data-ttu-id="53f33-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> является основным компонентом, который библиотеки и приложения используют для инициализации, построения и запуска узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="53f33-648">Параметры</span><span class="sxs-lookup"><span data-stu-id="53f33-648">Options</span></span>

<span data-ttu-id="53f33-649"><xref:Microsoft.Extensions.Hosting.HostOptions> настраивает параметры для <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="53f33-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="53f33-650">Время ожидания завершения работы</span><span class="sxs-lookup"><span data-stu-id="53f33-650">Shutdown timeout</span></span>

<span data-ttu-id="53f33-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="53f33-652">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="53f33-652">The default value is five seconds.</span></span>

<span data-ttu-id="53f33-653">Следующий пример конфигурации в `Program.Main` увеличивает значение времени ожидания завершения работы по умолчанию с 5 до 20 секунд.</span><span class="sxs-lookup"><span data-stu-id="53f33-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="53f33-654">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="53f33-654">Default services</span></span>

<span data-ttu-id="53f33-655">Во время инициализации узла регистрируются следующие службы:</span><span class="sxs-lookup"><span data-stu-id="53f33-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="53f33-656">[Окружение](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="53f33-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="53f33-657">[Конфигурация](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="53f33-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="53f33-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="53f33-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="53f33-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="53f33-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="53f33-660">[Параметры](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="53f33-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="53f33-661">[Ведение журнала](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="53f33-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="53f33-662">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="53f33-662">Host configuration</span></span>

<span data-ttu-id="53f33-663">Существуют следующие подходы для создания конфигурации узла:</span><span class="sxs-lookup"><span data-stu-id="53f33-663">Host configuration is created by:</span></span>

* <span data-ttu-id="53f33-664">вызов методов расширения в <xref:Microsoft.Extensions.Hosting.IHostBuilder> для задания [корневой папки содержимого](#content-root) и [среды](#environment);</span><span class="sxs-lookup"><span data-stu-id="53f33-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="53f33-665">чтение конфигурации из поставщиков конфигурации в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="53f33-666">Методы расширения</span><span class="sxs-lookup"><span data-stu-id="53f33-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="53f33-667">Ключ приложения (имя)</span><span class="sxs-lookup"><span data-stu-id="53f33-667">Application key (name)</span></span>

<span data-ttu-id="53f33-668">Свойство [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="53f33-669">Чтобы явно задать значение, используйте [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey).</span><span class="sxs-lookup"><span data-stu-id="53f33-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="53f33-670">**Ключ:** `applicationName`</span><span class="sxs-lookup"><span data-stu-id="53f33-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="53f33-671">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-671">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-672">**По умолчанию**: имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="53f33-673">**Задается с помощью**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="53f33-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="53f33-674">**Переменная среды**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="53f33-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="53f33-675">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="53f33-675">Content root</span></span>

<span data-ttu-id="53f33-676">Этот параметр определяет, где узел начинает искать файлы содержимого.</span><span class="sxs-lookup"><span data-stu-id="53f33-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="53f33-677">**Ключ:** `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="53f33-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="53f33-678">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-678">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-679">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="53f33-680">**Задается с помощью**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="53f33-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="53f33-681">**Переменная среды**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="53f33-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="53f33-682">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="53f33-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="53f33-683">Дополнительные сведения можно найти в разделе [Корневой каталог содержимого](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="53f33-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="53f33-684">Среда</span><span class="sxs-lookup"><span data-stu-id="53f33-684">Environment</span></span>

<span data-ttu-id="53f33-685">Задает [среду](xref:fundamentals/environments) приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="53f33-686">**Ключ:** `environment`</span><span class="sxs-lookup"><span data-stu-id="53f33-686">**Key**: `environment`</span></span>  
<span data-ttu-id="53f33-687">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="53f33-687">**Type**: `string`</span></span>  
<span data-ttu-id="53f33-688">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="53f33-688">**Default**: `Production`</span></span>  
<span data-ttu-id="53f33-689">**Задается с помощью**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="53f33-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="53f33-690">**Переменная среды**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="53f33-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="53f33-691">В качестве среды можно указать любое значение.</span><span class="sxs-lookup"><span data-stu-id="53f33-691">The environment can be set to any value.</span></span> <span data-ttu-id="53f33-692">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="53f33-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="53f33-693">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="53f33-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="53f33-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="53f33-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="53f33-695">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="53f33-696">Конфигурация узла применяется для инициализации <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> в целях использования в процессе сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="53f33-697">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-698">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="53f33-699">Поставщики не включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-699">No providers are included by default.</span></span> <span data-ttu-id="53f33-700">Необходимо явно указать поставщики конфигурации, требуемые приложению в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, в том числе:</span><span class="sxs-lookup"><span data-stu-id="53f33-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="53f33-701">конфигурация файла (например, из файла *hostsettings.json*);</span><span class="sxs-lookup"><span data-stu-id="53f33-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="53f33-702">конфигурация переменной среды;</span><span class="sxs-lookup"><span data-stu-id="53f33-702">Environment variable configuration.</span></span>
* <span data-ttu-id="53f33-703">конфигурация аргумента командной строки;</span><span class="sxs-lookup"><span data-stu-id="53f33-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="53f33-704">другие необходимые поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="53f33-704">Any other required configuration providers.</span></span>

<span data-ttu-id="53f33-705">Конфигурация файла узла включается путем указания основного пути приложения с помощью `SetBasePath` и последующего вызова одного из [поставщиков конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="53f33-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="53f33-706">В примере приложения используется JSON-файл *hostsettings.json* и вызывается метод <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> для использования параметров конфигурации узла файла.</span><span class="sxs-lookup"><span data-stu-id="53f33-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="53f33-707">Чтобы добавить [конфигурацию переменной среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) узла, вызовите метод <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в построителе узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="53f33-708">`AddEnvironmentVariables` принимает необязательный определяемый пользователем префикс.</span><span class="sxs-lookup"><span data-stu-id="53f33-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="53f33-709">В примере приложения используется префикс `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="53f33-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="53f33-710">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="53f33-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="53f33-711">После настройки узла в примере приложения значение переменной среды для `PREFIX_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="53f33-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="53f33-712">Во время разработки с использованием [Visual Studio](https://visualstudio.microsoft.com) или запуска приложения с помощью `dotnet run` переменные среды можно задать в файле *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="53f33-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="53f33-713">В [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json* во время разработки.</span><span class="sxs-lookup"><span data-stu-id="53f33-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="53f33-714">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="53f33-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="53f33-715">[Конфигурация командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) добавляется путем вызова метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="53f33-716">Конфигурация командной строки добавляется в последнюю очередь, чтобы разрешить аргументам командной строки переопределять конфигурацию, предоставленную предыдущими поставщиками конфигурации.</span><span class="sxs-lookup"><span data-stu-id="53f33-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="53f33-717">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="53f33-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="53f33-718">Дополнительную конфигурацию можно указать с помощью ключей [applicationName](#application-key-name) и [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="53f33-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="53f33-719">Пример конфигурации `HostBuilder` с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="53f33-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="53f33-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="53f33-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="53f33-721">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в реализации <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="53f33-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="53f33-722">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="53f33-723">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="53f33-724">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="53f33-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="53f33-725">Конфигурация, созданная с помощью <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и в <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="53f33-726">Конфигурация приложения автоматически получает конфигурацию узла, предоставленную с помощью [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="53f33-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="53f33-727">Пример конфигурации приложения с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="53f33-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="53f33-728">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="53f33-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="53f33-729">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="53f33-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="53f33-730">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="53f33-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="53f33-731">Чтобы переместить файлы параметров в выходной каталог, укажите файлы параметров как [элементы проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="53f33-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="53f33-732">Пример приложения перемещает свои файлы параметров приложения JSON и *hostsettings.json* со следующим элементом `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="53f33-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="53f33-733">Для таких методов расширения конфигурации, как <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> и <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, необходимы дополнительные пакеты NuGet, такие как [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) и [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="53f33-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="53f33-734">Если приложение не использует [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), эти пакеты нужно добавить в проект в дополнение к основному пакету [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="53f33-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="53f33-735">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="53f33-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="53f33-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="53f33-736">ConfigureServices</span></span>

<span data-ttu-id="53f33-737">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> добавляет службы в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="53f33-738">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="53f33-739">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="53f33-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="53f33-740">Для получения дополнительной информации см. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="53f33-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="53f33-741">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует метод расширения `AddHostedService` для добавления службы для событий времени жизни (`LifetimeEventsHostedService`) и синхронизированной фоновой задачи (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="53f33-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="53f33-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="53f33-742">ConfigureLogging</span></span>

<span data-ttu-id="53f33-743">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> добавляет делегат для настройки указанного интерфейса <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="53f33-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="53f33-744">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="53f33-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-745">UseConsoleLifetime</span></span>

<span data-ttu-id="53f33-746">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>//SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="53f33-747">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="53f33-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="53f33-748">Класс `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` предварительно зарегистрирован и является реализацией времени жизни по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="53f33-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="53f33-749">Используется то время жизни, которое зарегистрировано последним.</span><span class="sxs-lookup"><span data-stu-id="53f33-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="53f33-750">Конфигурация контейнера</span><span class="sxs-lookup"><span data-stu-id="53f33-750">Container configuration</span></span>

<span data-ttu-id="53f33-751">Для поддержки подключения к другим контейнерам узел может принимать <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="53f33-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="53f33-752">Предоставляет фабрику, не являющуюся частью регистрации контейнера внедрения зависимостей, а являющуюся встроенной функцией узла, которая используется для создания конкретных контейнеров внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="53f33-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="53f33-753">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) переопределяет фабрику по умолчанию, используемую для создания поставщика службы приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="53f33-754">Пользовательская конфигурация контейнера управляется методом <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="53f33-755">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> обеспечивает возможности строго типизированной настройки контейнера на основе базового API узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="53f33-756">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="53f33-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="53f33-757">Создание контейнера службы для приложения:</span><span class="sxs-lookup"><span data-stu-id="53f33-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="53f33-758">Настройка фабрики контейнера службы:</span><span class="sxs-lookup"><span data-stu-id="53f33-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="53f33-759">Использование фабрики и настройка пользовательского контейнера служб для приложения:</span><span class="sxs-lookup"><span data-stu-id="53f33-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="53f33-760">Расширение среды</span><span class="sxs-lookup"><span data-stu-id="53f33-760">Extensibility</span></span>

<span data-ttu-id="53f33-761">Расширяемость узла выполняется с помощью методов расширения класса <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="53f33-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="53f33-762">В следующем примере показано, как метод расширения расширяет реализацию класса <xref:Microsoft.Extensions.Hosting.IHostBuilder> с помощью класса [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), пример которого приведен в статье <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="53f33-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="53f33-763">Приложение устанавливает метод расширения `UseHostedService`, чтобы зарегистрировать размещенную службу, переданную в `T`:</span><span class="sxs-lookup"><span data-stu-id="53f33-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="53f33-764">Управление узлом</span><span class="sxs-lookup"><span data-stu-id="53f33-764">Manage the host</span></span>

<span data-ttu-id="53f33-765">Реализация <xref:Microsoft.Extensions.Hosting.IHost> отвечает за запуск и остановку реализаций <xref:Microsoft.Extensions.Hosting.IHostedService>, которые зарегистрированы в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="53f33-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="53f33-766">Выполнить</span><span class="sxs-lookup"><span data-stu-id="53f33-766">Run</span></span>

<span data-ttu-id="53f33-767">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена:</span><span class="sxs-lookup"><span data-stu-id="53f33-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="53f33-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-768">RunAsync</span></span>

<span data-ttu-id="53f33-769">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершение работы:</span><span class="sxs-lookup"><span data-stu-id="53f33-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="53f33-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-770">RunConsoleAsync</span></span>

<span data-ttu-id="53f33-771">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="53f33-772">Start и StopAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-772">Start and StopAsync</span></span>

<span data-ttu-id="53f33-773">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="53f33-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="53f33-774">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="53f33-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="53f33-775">StartAsync и StopAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="53f33-776">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="53f33-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="53f33-777">Метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> останавливает приложение.</span><span class="sxs-lookup"><span data-stu-id="53f33-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="53f33-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="53f33-778">WaitForShutdown</span></span>

<span data-ttu-id="53f33-779">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> запускается с помощью <xref:Microsoft.Extensions.Hosting.IHostLifetime>, например `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (прослушивает <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="53f33-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="53f33-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> вызывает <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="53f33-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="53f33-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="53f33-782">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="53f33-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="53f33-783">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="53f33-783">External control</span></span>

<span data-ttu-id="53f33-784">Внешнее управление узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="53f33-784">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="53f33-785">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="53f33-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="53f33-786">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="53f33-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="53f33-787">Интерфейс IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="53f33-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="53f33-788">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет сведения о среде размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="53f33-789">Чтобы получить интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="53f33-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="53f33-790">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="53f33-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="53f33-791">Интерфейс IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="53f33-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="53f33-792">Интерфейс <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> позволяет выполнять действия после запуска и завершения работы, включая запросы о нормальном завершении работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="53f33-793">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов <xref:System.Action>, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="53f33-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="53f33-794">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="53f33-794">Cancellation Token</span></span> | <span data-ttu-id="53f33-795">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="53f33-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="53f33-796">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="53f33-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="53f33-797">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="53f33-798">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="53f33-798">All requests should be processed.</span></span> <span data-ttu-id="53f33-799">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="53f33-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="53f33-800">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="53f33-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="53f33-801">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="53f33-801">Requests may still be processing.</span></span> <span data-ttu-id="53f33-802">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="53f33-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="53f33-803">Конструктор внедряет службу <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> в любой класс.</span><span class="sxs-lookup"><span data-stu-id="53f33-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="53f33-804">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует внедрение в конструкторе в класс `LifetimeEventsHostedService` (реализацию <xref:Microsoft.Extensions.Hosting.IHostedService>) для регистрации событий.</span><span class="sxs-lookup"><span data-stu-id="53f33-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="53f33-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="53f33-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="53f33-806">Метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="53f33-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="53f33-807">Следующий класс использует <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="53f33-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="53f33-808">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="53f33-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
