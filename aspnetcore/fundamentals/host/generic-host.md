---
title: Универсальный узел .NET
author: rick-anderson
description: Сведения об универсальном узле .NET Core, который отвечает за запуск приложений и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488778"
---
# <a name="net-generic-host"></a><span data-ttu-id="18b30-103">Универсальный узел .NET</span><span class="sxs-lookup"><span data-stu-id="18b30-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="18b30-104">Шаблоны ASP.NET Core создают .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18b30-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="18b30-105">Определение узла</span><span class="sxs-lookup"><span data-stu-id="18b30-105">Host definition</span></span>

<span data-ttu-id="18b30-106">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="18b30-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="18b30-107">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="18b30-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="18b30-108">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="18b30-108">Logging</span></span>
* <span data-ttu-id="18b30-109">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="18b30-109">Configuration</span></span>
* <span data-ttu-id="18b30-110">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="18b30-110">`IHostedService` implementations</span></span>

<span data-ttu-id="18b30-111">После запуска узла он вызывает `IHostedService.StartAsync` в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, которую найдет в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="18b30-112">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="18b30-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="18b30-113">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="18b30-114">Создание узла</span><span class="sxs-lookup"><span data-stu-id="18b30-114">Set up a host</span></span>

<span data-ttu-id="18b30-115">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="18b30-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="18b30-116">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="18b30-116">The `Main` method:</span></span>

* <span data-ttu-id="18b30-117">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="18b30-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="18b30-118">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="18b30-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="18b30-119">Веб-шаблоны ASP.NET Core создают следующий код для создания универсального узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="18b30-120">Следующий код создает универсальный узел, используя рабочую нагрузку, отличную от HTTP.</span><span class="sxs-lookup"><span data-stu-id="18b30-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="18b30-121">Реализация `IHostedService` добавляется в контейнер DI:</span><span class="sxs-lookup"><span data-stu-id="18b30-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="18b30-122">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="18b30-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="18b30-123">Приведенный выше код создается шаблонами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b30-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="18b30-124">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="18b30-125">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="18b30-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="18b30-126">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="18b30-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="18b30-127">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="18b30-127">Default builder settings</span></span>

<span data-ttu-id="18b30-128">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="18b30-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="18b30-129">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="18b30-130">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="18b30-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="18b30-131">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="18b30-132">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="18b30-132">Command-line arguments.</span></span>
* <span data-ttu-id="18b30-133">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="18b30-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="18b30-134">*appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="18b30-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="18b30-135">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="18b30-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="18b30-136">[диспетчер секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="18b30-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="18b30-137">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-137">Environment variables.</span></span>
  * <span data-ttu-id="18b30-138">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="18b30-138">Command-line arguments.</span></span>
* <span data-ttu-id="18b30-139">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="18b30-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="18b30-140">Консоль</span><span class="sxs-lookup"><span data-stu-id="18b30-140">Console</span></span>
  * <span data-ttu-id="18b30-141">Отладка</span><span class="sxs-lookup"><span data-stu-id="18b30-141">Debug</span></span>
  * <span data-ttu-id="18b30-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="18b30-142">EventSource</span></span>
  * <span data-ttu-id="18b30-143">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="18b30-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="18b30-144">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="18b30-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="18b30-145">Метод `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="18b30-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="18b30-146">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="18b30-147">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="18b30-148">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="18b30-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="18b30-149">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="18b30-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="18b30-150">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="18b30-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="18b30-151">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="18b30-151">Enables IIS integration.</span></span> <span data-ttu-id="18b30-152">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="18b30-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="18b30-153">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="18b30-154">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="18b30-154">Framework-provided services</span></span>

<span data-ttu-id="18b30-155">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="18b30-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="18b30-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="18b30-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="18b30-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="18b30-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="18b30-159">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="18b30-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="18b30-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="18b30-161">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="18b30-162">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="18b30-163">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="18b30-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="18b30-164">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="18b30-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="18b30-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-165">IHostLifetime</span></span>

<span data-ttu-id="18b30-166">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="18b30-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="18b30-167">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="18b30-167">The last implementation registered is used.</span></span>

<span data-ttu-id="18b30-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="18b30-169">`ConsoleLifetime`.</span><span class="sxs-lookup"><span data-stu-id="18b30-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="18b30-170">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="18b30-171">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="18b30-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="18b30-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="18b30-172">IHostEnvironment</span></span>

<span data-ttu-id="18b30-173">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="18b30-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="18b30-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="18b30-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="18b30-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="18b30-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="18b30-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="18b30-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="18b30-177">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="18b30-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="18b30-178">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="18b30-178">Host configuration</span></span>

<span data-ttu-id="18b30-179">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="18b30-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="18b30-180">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="18b30-181">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="18b30-182">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="18b30-183">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-184">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="18b30-185">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="18b30-186">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="18b30-187">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="18b30-188">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="18b30-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="18b30-189">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="18b30-190">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="18b30-190">App configuration</span></span>

<span data-ttu-id="18b30-191">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="18b30-192">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-193">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="18b30-194">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="18b30-195">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="18b30-196">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="18b30-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="18b30-197">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="18b30-197">Settings for all app types</span></span>

<span data-ttu-id="18b30-198">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="18b30-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="18b30-199">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="18b30-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="18b30-200">ApplicationName</span></span>

<span data-ttu-id="18b30-201">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="18b30-202">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="18b30-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="18b30-203">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-203">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-204">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="18b30-205">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="18b30-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="18b30-206">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="18b30-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-207">ContentRoot</span></span>

<span data-ttu-id="18b30-208">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="18b30-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="18b30-209">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="18b30-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="18b30-210">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="18b30-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="18b30-211">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-211">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-212">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="18b30-213">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="18b30-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="18b30-214">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="18b30-215">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="18b30-215">For more information, see:</span></span>

* [<span data-ttu-id="18b30-216">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="18b30-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="18b30-217">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="18b30-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="18b30-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="18b30-218">EnvironmentName</span></span>

<span data-ttu-id="18b30-219">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="18b30-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="18b30-220">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="18b30-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="18b30-221">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="18b30-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="18b30-222">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="18b30-222">**Key**: `environment`</span></span>  
<span data-ttu-id="18b30-223">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-223">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-224">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="18b30-224">**Default**: `Production`</span></span>  
<span data-ttu-id="18b30-225">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="18b30-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="18b30-226">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="18b30-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="18b30-227">ShutdownTimeout</span></span>

<span data-ttu-id="18b30-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="18b30-229">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="18b30-229">The default value is five seconds.</span></span>  <span data-ttu-id="18b30-230">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="18b30-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="18b30-231">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="18b30-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="18b30-232">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="18b30-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="18b30-233">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="18b30-234">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="18b30-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="18b30-235">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="18b30-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="18b30-236">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="18b30-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="18b30-237">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="18b30-237">**Type**: `int`</span></span>  
<span data-ttu-id="18b30-238">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="18b30-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="18b30-239">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="18b30-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="18b30-240">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="18b30-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="18b30-241">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="18b30-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="18b30-242">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="18b30-242">Settings for web apps</span></span>

<span data-ttu-id="18b30-243">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="18b30-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="18b30-244">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="18b30-245">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="18b30-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="18b30-246">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="18b30-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="18b30-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="18b30-247">CaptureStartupErrors</span></span>

<span data-ttu-id="18b30-248">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="18b30-249">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="18b30-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="18b30-250">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="18b30-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="18b30-251">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-252">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="18b30-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="18b30-253">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="18b30-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="18b30-254">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="18b30-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="18b30-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="18b30-255">DetailedErrors</span></span>

<span data-ttu-id="18b30-256">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="18b30-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="18b30-257">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="18b30-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="18b30-258">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-259">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="18b30-259">**Default**: `false`</span></span>  
<span data-ttu-id="18b30-260">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="18b30-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="18b30-261">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="18b30-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="18b30-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="18b30-263">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="18b30-264">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="18b30-265">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="18b30-266">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="18b30-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="18b30-267">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-267">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-268">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="18b30-268">**Default**: Empty string</span></span>  
<span data-ttu-id="18b30-269">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="18b30-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="18b30-270">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="18b30-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="18b30-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="18b30-272">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="18b30-273">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="18b30-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="18b30-274">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-274">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-275">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="18b30-275">**Default**: Empty string</span></span>  
<span data-ttu-id="18b30-276">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="18b30-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="18b30-277">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="18b30-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="18b30-278">HTTPS_Port</span></span>

<span data-ttu-id="18b30-279">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="18b30-279">The HTTPS redirect port.</span></span> <span data-ttu-id="18b30-280">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="18b30-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="18b30-281">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="18b30-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="18b30-282">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-282">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-283">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="18b30-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="18b30-284">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="18b30-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="18b30-285">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="18b30-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="18b30-286">PreferHostingUrls</span></span>

<span data-ttu-id="18b30-287">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="18b30-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="18b30-288">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="18b30-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="18b30-289">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-290">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="18b30-290">**Default**: `true`</span></span>  
<span data-ttu-id="18b30-291">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="18b30-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="18b30-292">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="18b30-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="18b30-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="18b30-293">PreventHostingStartup</span></span>

<span data-ttu-id="18b30-294">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="18b30-295">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="18b30-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="18b30-296">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="18b30-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="18b30-297">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-298">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="18b30-298">**Default**: `false`</span></span>  
<span data-ttu-id="18b30-299">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="18b30-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="18b30-300">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="18b30-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="18b30-301">StartupAssembly</span></span>

<span data-ttu-id="18b30-302">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18b30-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="18b30-303">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="18b30-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="18b30-304">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-304">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-305">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="18b30-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="18b30-306">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="18b30-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="18b30-307">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="18b30-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="18b30-308">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="18b30-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="18b30-309">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="18b30-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="18b30-310">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="18b30-310">URLs</span></span>

<span data-ttu-id="18b30-311">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="18b30-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="18b30-312">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="18b30-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="18b30-313">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="18b30-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="18b30-314">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="18b30-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="18b30-315">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="18b30-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="18b30-316">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="18b30-316">**Key**: `urls`</span></span>  
<span data-ttu-id="18b30-317">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-317">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-318">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="18b30-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="18b30-319">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="18b30-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="18b30-320">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="18b30-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="18b30-321">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="18b30-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="18b30-322">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="18b30-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="18b30-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-323">WebRoot</span></span>

<span data-ttu-id="18b30-324">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="18b30-325">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="18b30-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="18b30-326">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="18b30-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="18b30-327">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-327">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-328">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="18b30-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="18b30-329">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="18b30-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="18b30-330">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="18b30-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="18b30-331">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="18b30-332">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="18b30-332">For more information, see:</span></span>

* [<span data-ttu-id="18b30-333">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="18b30-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="18b30-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="18b30-335">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="18b30-335">Manage the host lifetime</span></span>

<span data-ttu-id="18b30-336">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="18b30-337">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="18b30-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="18b30-338">Выполнить</span><span class="sxs-lookup"><span data-stu-id="18b30-338">Run</span></span>

<span data-ttu-id="18b30-339">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="18b30-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="18b30-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-340">RunAsync</span></span>

<span data-ttu-id="18b30-341">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="18b30-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-342">RunConsoleAsync</span></span>

<span data-ttu-id="18b30-343">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="18b30-344">Начало</span><span class="sxs-lookup"><span data-stu-id="18b30-344">Start</span></span>

<span data-ttu-id="18b30-345">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="18b30-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="18b30-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-346">StartAsync</span></span>

<span data-ttu-id="18b30-347">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="18b30-348">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="18b30-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="18b30-349">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="18b30-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="18b30-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-350">StopAsync</span></span>

<span data-ttu-id="18b30-351">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="18b30-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="18b30-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="18b30-352">WaitForShutdown</span></span>

<span data-ttu-id="18b30-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="18b30-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="18b30-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="18b30-355">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="18b30-356">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="18b30-356">External control</span></span>

<span data-ttu-id="18b30-357">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="18b30-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="18b30-358">Приложения ASP.NET Core настраивают и запускают узел.</span><span class="sxs-lookup"><span data-stu-id="18b30-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="18b30-359">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="18b30-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="18b30-360">В этой статье рассматривается универсальный узел ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), используемый для приложений, которые не обрабатывают запросы HTTP.</span><span class="sxs-lookup"><span data-stu-id="18b30-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="18b30-361">Универсальный узел предназначен для отделения конвейера HTTP от API веб-узла, чтобы можно было иметь больше сценариев узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="18b30-362">Обмен сообщениями, фоновые задачи и другие рабочие нагрузки, не связанные с HTTP, используют перекрестные возможности универсальных узлов, такие как конфигурация, внедрение зависимости (DI) и ведение журналов.</span><span class="sxs-lookup"><span data-stu-id="18b30-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="18b30-363">Универсальный узел является новой возможностью ASP.NET Core 2.1 и не подходит для сценариев с веб-размещением.</span><span class="sxs-lookup"><span data-stu-id="18b30-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="18b30-364">Сведения о сценариях с веб-размещением см. в статье о [веб-узле](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="18b30-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="18b30-365">Универсальный узел заменит веб-узел в будущих версиях. Он будет выступать основным узлом API для сценариев HTTP и "не HTTP".</span><span class="sxs-lookup"><span data-stu-id="18b30-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="18b30-366">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18b30-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="18b30-367">При выполнении примера приложения в [Visual Studio Code](https://code.visualstudio.com/) используйте *внешний или встроенный терминал*.</span><span class="sxs-lookup"><span data-stu-id="18b30-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="18b30-368">Не выполняйте пример в `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="18b30-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="18b30-369">Настройка консоли в Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="18b30-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="18b30-370">Откройте файл *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="18b30-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="18b30-371">В разделе конфигурации **.NET Core Launch (console)** найдите параметр **console**.</span><span class="sxs-lookup"><span data-stu-id="18b30-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="18b30-372">Измените значение на `externalTerminal` или `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="18b30-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="18b30-373">Вступление</span><span class="sxs-lookup"><span data-stu-id="18b30-373">Introduction</span></span>

<span data-ttu-id="18b30-374">Библиотека универсального узла находится в пространстве имен <xref:Microsoft.Extensions.Hosting> и включена в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="18b30-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="18b30-375">Пакет `Microsoft.Extensions.Hosting` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="18b30-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="18b30-376"><xref:Microsoft.Extensions.Hosting.IHostedService> — это точка входа для выполнения кода.</span><span class="sxs-lookup"><span data-stu-id="18b30-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="18b30-377">Каждая реализация <xref:Microsoft.Extensions.Hosting.IHostedService> выполняется в порядке [регистрации служб в ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="18b30-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="18b30-378">Метод <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> вызывается в каждом <xref:Microsoft.Extensions.Hosting.IHostedService> при запуске узла, а метод <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> вызывается в порядке, обратном регистрации, когда узел корректно завершает работу.</span><span class="sxs-lookup"><span data-stu-id="18b30-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="18b30-379">Создание узла</span><span class="sxs-lookup"><span data-stu-id="18b30-379">Set up a host</span></span>

<span data-ttu-id="18b30-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> является основным компонентом, который библиотеки и приложения используют для инициализации, построения и запуска узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="18b30-381">Параметры</span><span class="sxs-lookup"><span data-stu-id="18b30-381">Options</span></span>

<span data-ttu-id="18b30-382"><xref:Microsoft.Extensions.Hosting.HostOptions> настраивает параметры для <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="18b30-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="18b30-383">Время ожидания завершения работы</span><span class="sxs-lookup"><span data-stu-id="18b30-383">Shutdown timeout</span></span>

<span data-ttu-id="18b30-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="18b30-385">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="18b30-385">The default value is five seconds.</span></span>

<span data-ttu-id="18b30-386">Следующий пример конфигурации в `Program.Main` увеличивает значение времени ожидания завершения работы по умолчанию с 5 до 20 секунд.</span><span class="sxs-lookup"><span data-stu-id="18b30-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="18b30-387">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="18b30-387">Default services</span></span>

<span data-ttu-id="18b30-388">Во время инициализации узла регистрируются следующие службы:</span><span class="sxs-lookup"><span data-stu-id="18b30-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="18b30-389">[Окружение](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="18b30-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="18b30-390">[Конфигурация](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="18b30-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="18b30-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="18b30-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="18b30-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="18b30-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="18b30-393">[Параметры](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="18b30-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="18b30-394">[Ведение журнала](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="18b30-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="18b30-395">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="18b30-395">Host configuration</span></span>

<span data-ttu-id="18b30-396">Существуют следующие подходы для создания конфигурации узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-396">Host configuration is created by:</span></span>

* <span data-ttu-id="18b30-397">вызов методов расширения в <xref:Microsoft.Extensions.Hosting.IHostBuilder> для задания [корневой папки содержимого](#content-root) и [среды](#environment);</span><span class="sxs-lookup"><span data-stu-id="18b30-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="18b30-398">чтение конфигурации из поставщиков конфигурации в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="18b30-399">Методы расширения</span><span class="sxs-lookup"><span data-stu-id="18b30-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="18b30-400">Ключ приложения (имя)</span><span class="sxs-lookup"><span data-stu-id="18b30-400">Application key (name)</span></span>

<span data-ttu-id="18b30-401">Свойство [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="18b30-402">Чтобы явно задать значение, используйте [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey).</span><span class="sxs-lookup"><span data-stu-id="18b30-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="18b30-403">**Ключ:** `applicationName`</span><span class="sxs-lookup"><span data-stu-id="18b30-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="18b30-404">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-404">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-405">**По умолчанию**: имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="18b30-406">**Задается с помощью**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="18b30-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="18b30-407">**Переменная среды**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="18b30-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="18b30-408">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="18b30-408">Content root</span></span>

<span data-ttu-id="18b30-409">Этот параметр определяет, где узел начинает искать файлы содержимого.</span><span class="sxs-lookup"><span data-stu-id="18b30-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="18b30-410">**Ключ:** `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="18b30-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="18b30-411">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-411">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-412">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="18b30-413">**Задается с помощью**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="18b30-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="18b30-414">**Переменная среды**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="18b30-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="18b30-415">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="18b30-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="18b30-416">Дополнительные сведения можно найти в разделе [Корневой каталог содержимого](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="18b30-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="18b30-417">Среда</span><span class="sxs-lookup"><span data-stu-id="18b30-417">Environment</span></span>

<span data-ttu-id="18b30-418">Задает [среду](xref:fundamentals/environments) приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="18b30-419">**Ключ:** `environment`</span><span class="sxs-lookup"><span data-stu-id="18b30-419">**Key**: `environment`</span></span>  
<span data-ttu-id="18b30-420">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-420">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-421">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="18b30-421">**Default**: `Production`</span></span>  
<span data-ttu-id="18b30-422">**Задается с помощью**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="18b30-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="18b30-423">**Переменная среды**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="18b30-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="18b30-424">В качестве среды можно указать любое значение.</span><span class="sxs-lookup"><span data-stu-id="18b30-424">The environment can be set to any value.</span></span> <span data-ttu-id="18b30-425">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="18b30-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="18b30-426">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="18b30-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="18b30-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="18b30-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="18b30-428">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="18b30-429">Конфигурация узла применяется для инициализации <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> в целях использования в процессе сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="18b30-430">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-431">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="18b30-432">Поставщики не включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-432">No providers are included by default.</span></span> <span data-ttu-id="18b30-433">Необходимо явно указать поставщики конфигурации, требуемые приложению в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, в том числе:</span><span class="sxs-lookup"><span data-stu-id="18b30-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="18b30-434">конфигурация файла (например, из файла *hostsettings.json*);</span><span class="sxs-lookup"><span data-stu-id="18b30-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="18b30-435">конфигурация переменной среды;</span><span class="sxs-lookup"><span data-stu-id="18b30-435">Environment variable configuration.</span></span>
* <span data-ttu-id="18b30-436">конфигурация аргумента командной строки;</span><span class="sxs-lookup"><span data-stu-id="18b30-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="18b30-437">другие необходимые поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="18b30-437">Any other required configuration providers.</span></span>

<span data-ttu-id="18b30-438">Конфигурация файла узла включается путем указания основного пути приложения с помощью `SetBasePath` и последующего вызова одного из [поставщиков конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="18b30-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="18b30-439">В примере приложения используется JSON-файл *hostsettings.json* и вызывается метод <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> для использования параметров конфигурации узла файла.</span><span class="sxs-lookup"><span data-stu-id="18b30-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="18b30-440">Чтобы добавить [конфигурацию переменной среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) узла, вызовите метод <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в построителе узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="18b30-441">`AddEnvironmentVariables` принимает необязательный определяемый пользователем префикс.</span><span class="sxs-lookup"><span data-stu-id="18b30-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="18b30-442">В примере приложения используется префикс `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="18b30-443">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="18b30-444">После настройки узла в примере приложения значение переменной среды для `PREFIX_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="18b30-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="18b30-445">Во время разработки с использованием [Visual Studio](https://visualstudio.microsoft.com) или запуска приложения с помощью `dotnet run` переменные среды можно задать в файле *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="18b30-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="18b30-446">В [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json* во время разработки.</span><span class="sxs-lookup"><span data-stu-id="18b30-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="18b30-447">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="18b30-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="18b30-448">[Конфигурация командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) добавляется путем вызова метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="18b30-449">Конфигурация командной строки добавляется в последнюю очередь, чтобы разрешить аргументам командной строки переопределять конфигурацию, предоставленную предыдущими поставщиками конфигурации.</span><span class="sxs-lookup"><span data-stu-id="18b30-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="18b30-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="18b30-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="18b30-451">Дополнительную конфигурацию можно указать с помощью ключей [applicationName](#application-key-name) и [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="18b30-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="18b30-452">Пример конфигурации `HostBuilder` с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="18b30-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="18b30-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="18b30-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="18b30-454">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в реализации <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18b30-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="18b30-455">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="18b30-456">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-457">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="18b30-458">Конфигурация, созданная с помощью <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и в <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="18b30-459">Конфигурация приложения автоматически получает конфигурацию узла, предоставленную с помощью [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="18b30-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="18b30-460">Пример конфигурации приложения с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="18b30-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="18b30-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="18b30-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="18b30-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="18b30-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="18b30-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="18b30-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="18b30-464">Чтобы переместить файлы параметров в выходной каталог, укажите файлы параметров как [элементы проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="18b30-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="18b30-465">Пример приложения перемещает свои файлы параметров приложения JSON и *hostsettings.json* со следующим элементом `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="18b30-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="18b30-466">Для таких методов расширения конфигурации, как <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> и <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, необходимы дополнительные пакеты NuGet, такие как [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) и [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="18b30-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="18b30-467">Если приложение не использует [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), эти пакеты нужно добавить в проект в дополнение к основному пакету [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="18b30-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="18b30-468">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="18b30-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="18b30-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="18b30-469">ConfigureServices</span></span>

<span data-ttu-id="18b30-470">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> добавляет службы в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="18b30-471">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="18b30-472">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="18b30-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="18b30-473">Для получения дополнительной информации см. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="18b30-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="18b30-474">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует метод расширения `AddHostedService` для добавления службы для событий времени жизни (`LifetimeEventsHostedService`) и синхронизированной фоновой задачи (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="18b30-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="18b30-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="18b30-475">ConfigureLogging</span></span>

<span data-ttu-id="18b30-476">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> добавляет делегат для настройки указанного интерфейса <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18b30-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="18b30-477">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="18b30-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-478">UseConsoleLifetime</span></span>

<span data-ttu-id="18b30-479">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>//SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="18b30-480">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="18b30-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="18b30-481">Класс `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` предварительно зарегистрирован и является реализацией времени жизни по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="18b30-482">Используется то время жизни, которое зарегистрировано последним.</span><span class="sxs-lookup"><span data-stu-id="18b30-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="18b30-483">Конфигурация контейнера</span><span class="sxs-lookup"><span data-stu-id="18b30-483">Container configuration</span></span>

<span data-ttu-id="18b30-484">Для поддержки подключения к другим контейнерам узел может принимать <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="18b30-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="18b30-485">Предоставляет фабрику, не являющуюся частью регистрации контейнера внедрения зависимостей, а являющуюся встроенной функцией узла, которая используется для создания конкретных контейнеров внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="18b30-486">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) переопределяет фабрику по умолчанию, используемую для создания поставщика службы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="18b30-487">Пользовательская конфигурация контейнера управляется методом <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="18b30-488">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> обеспечивает возможности строго типизированной настройки контейнера на основе базового API узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="18b30-489">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="18b30-490">Создание контейнера службы для приложения:</span><span class="sxs-lookup"><span data-stu-id="18b30-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="18b30-491">Настройка фабрики контейнера службы:</span><span class="sxs-lookup"><span data-stu-id="18b30-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="18b30-492">Использование фабрики и настройка пользовательского контейнера служб для приложения:</span><span class="sxs-lookup"><span data-stu-id="18b30-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="18b30-493">Расширение среды</span><span class="sxs-lookup"><span data-stu-id="18b30-493">Extensibility</span></span>

<span data-ttu-id="18b30-494">Расширяемость узла выполняется с помощью методов расширения класса <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18b30-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="18b30-495">В следующем примере показано, как метод расширения расширяет реализацию класса <xref:Microsoft.Extensions.Hosting.IHostBuilder> с помощью класса [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), пример которого приведен в статье <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="18b30-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="18b30-496">Приложение устанавливает метод расширения `UseHostedService`, чтобы зарегистрировать размещенную службу, переданную в `T`:</span><span class="sxs-lookup"><span data-stu-id="18b30-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="18b30-497">Управление узлом</span><span class="sxs-lookup"><span data-stu-id="18b30-497">Manage the host</span></span>

<span data-ttu-id="18b30-498">Реализация <xref:Microsoft.Extensions.Hosting.IHost> отвечает за запуск и остановку реализаций <xref:Microsoft.Extensions.Hosting.IHostedService>, которые зарегистрированы в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="18b30-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="18b30-499">Выполнить</span><span class="sxs-lookup"><span data-stu-id="18b30-499">Run</span></span>

<span data-ttu-id="18b30-500">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена:</span><span class="sxs-lookup"><span data-stu-id="18b30-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="18b30-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-501">RunAsync</span></span>

<span data-ttu-id="18b30-502">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершение работы:</span><span class="sxs-lookup"><span data-stu-id="18b30-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="18b30-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-503">RunConsoleAsync</span></span>

<span data-ttu-id="18b30-504">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="18b30-505">Start и StopAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-505">Start and StopAsync</span></span>

<span data-ttu-id="18b30-506">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="18b30-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="18b30-507">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="18b30-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="18b30-508">StartAsync и StopAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="18b30-509">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="18b30-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="18b30-510">Метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> останавливает приложение.</span><span class="sxs-lookup"><span data-stu-id="18b30-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="18b30-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="18b30-511">WaitForShutdown</span></span>

<span data-ttu-id="18b30-512">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> запускается с помощью <xref:Microsoft.Extensions.Hosting.IHostLifetime>, например `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (прослушивает <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="18b30-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="18b30-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> вызывает <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="18b30-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="18b30-515">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="18b30-516">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="18b30-516">External control</span></span>

<span data-ttu-id="18b30-517">Внешнее управление узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="18b30-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="18b30-518">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="18b30-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="18b30-519">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="18b30-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="18b30-520">Интерфейс IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="18b30-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="18b30-521">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет сведения о среде размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="18b30-522">Чтобы получить интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="18b30-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="18b30-523">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="18b30-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="18b30-524">Интерфейс IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="18b30-525">Интерфейс <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> позволяет выполнять действия после запуска и завершения работы, включая запросы о нормальном завершении работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="18b30-526">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов <xref:System.Action>, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="18b30-527">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="18b30-527">Cancellation Token</span></span> | <span data-ttu-id="18b30-528">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="18b30-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="18b30-529">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="18b30-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="18b30-530">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="18b30-531">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="18b30-531">All requests should be processed.</span></span> <span data-ttu-id="18b30-532">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="18b30-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="18b30-533">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="18b30-534">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="18b30-534">Requests may still be processing.</span></span> <span data-ttu-id="18b30-535">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="18b30-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="18b30-536">Конструктор внедряет службу <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> в любой класс.</span><span class="sxs-lookup"><span data-stu-id="18b30-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="18b30-537">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует внедрение в конструкторе в класс `LifetimeEventsHostedService` (реализацию <xref:Microsoft.Extensions.Hosting.IHostedService>) для регистрации событий.</span><span class="sxs-lookup"><span data-stu-id="18b30-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="18b30-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="18b30-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="18b30-539">Метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="18b30-540">Следующий класс использует <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="18b30-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="18b30-541">Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="18b30-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="18b30-542">Определение узла</span><span class="sxs-lookup"><span data-stu-id="18b30-542">Host definition</span></span>

<span data-ttu-id="18b30-543">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="18b30-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="18b30-544">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="18b30-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="18b30-545">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="18b30-545">Logging</span></span>
* <span data-ttu-id="18b30-546">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="18b30-546">Configuration</span></span>
* <span data-ttu-id="18b30-547">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="18b30-547">`IHostedService` implementations</span></span>

<span data-ttu-id="18b30-548">После запуска узла он вызывает `IHostedService.StartAsync` в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, которую найдет в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="18b30-549">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="18b30-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="18b30-550">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="18b30-551">Создание узла</span><span class="sxs-lookup"><span data-stu-id="18b30-551">Set up a host</span></span>

<span data-ttu-id="18b30-552">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="18b30-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="18b30-553">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="18b30-553">The `Main` method:</span></span>

* <span data-ttu-id="18b30-554">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="18b30-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="18b30-555">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="18b30-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="18b30-556">Веб-шаблоны ASP.NET Core создают следующий код для создания узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="18b30-557">Следующий код создают нагрузку, отличную от HTTP, с одной реализацией `IHostedService`, добавленной в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="18b30-558">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="18b30-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="18b30-559">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="18b30-560">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="18b30-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="18b30-561">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="18b30-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="18b30-562">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="18b30-562">Default builder settings</span></span>

<span data-ttu-id="18b30-563">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="18b30-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="18b30-564">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="18b30-565">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="18b30-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="18b30-566">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="18b30-567">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="18b30-567">Command-line arguments.</span></span>
* <span data-ttu-id="18b30-568">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="18b30-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="18b30-569">*appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="18b30-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="18b30-570">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="18b30-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="18b30-571">[диспетчер секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="18b30-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="18b30-572">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-572">Environment variables.</span></span>
  * <span data-ttu-id="18b30-573">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="18b30-573">Command-line arguments.</span></span>
* <span data-ttu-id="18b30-574">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="18b30-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="18b30-575">Консоль</span><span class="sxs-lookup"><span data-stu-id="18b30-575">Console</span></span>
  * <span data-ttu-id="18b30-576">Отладка</span><span class="sxs-lookup"><span data-stu-id="18b30-576">Debug</span></span>
  * <span data-ttu-id="18b30-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="18b30-577">EventSource</span></span>
  * <span data-ttu-id="18b30-578">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="18b30-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="18b30-579">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="18b30-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="18b30-580">Метод `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="18b30-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="18b30-581">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="18b30-582">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="18b30-583">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="18b30-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="18b30-584">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="18b30-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="18b30-585">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="18b30-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="18b30-586">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="18b30-586">Enables IIS integration.</span></span> <span data-ttu-id="18b30-587">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="18b30-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="18b30-588">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="18b30-589">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="18b30-589">Framework-provided services</span></span>

<span data-ttu-id="18b30-590">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="18b30-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="18b30-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="18b30-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="18b30-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="18b30-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="18b30-594">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="18b30-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="18b30-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="18b30-596">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="18b30-597">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="18b30-598">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="18b30-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="18b30-599">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="18b30-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="18b30-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="18b30-600">IHostLifetime</span></span>

<span data-ttu-id="18b30-601">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="18b30-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="18b30-602">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="18b30-602">The last implementation registered is used.</span></span>

<span data-ttu-id="18b30-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="18b30-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="18b30-604">`ConsoleLifetime`.</span><span class="sxs-lookup"><span data-stu-id="18b30-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="18b30-605">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="18b30-606">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="18b30-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="18b30-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="18b30-607">IHostEnvironment</span></span>

<span data-ttu-id="18b30-608">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="18b30-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="18b30-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="18b30-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="18b30-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="18b30-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="18b30-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="18b30-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="18b30-612">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="18b30-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="18b30-613">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="18b30-613">Host configuration</span></span>

<span data-ttu-id="18b30-614">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="18b30-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="18b30-615">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="18b30-616">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="18b30-617">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="18b30-618">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-619">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="18b30-620">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="18b30-621">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="18b30-622">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="18b30-623">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="18b30-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="18b30-624">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="18b30-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="18b30-625">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="18b30-625">App configuration</span></span>

<span data-ttu-id="18b30-626">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="18b30-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="18b30-627">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="18b30-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="18b30-628">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="18b30-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="18b30-629">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="18b30-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="18b30-630">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="18b30-631">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="18b30-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="18b30-632">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="18b30-632">Settings for all app types</span></span>

<span data-ttu-id="18b30-633">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="18b30-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="18b30-634">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="18b30-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="18b30-635">ApplicationName</span></span>

<span data-ttu-id="18b30-636">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="18b30-637">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="18b30-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="18b30-638">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-638">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-639">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="18b30-640">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="18b30-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="18b30-641">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="18b30-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="18b30-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-642">ContentRoot</span></span>

<span data-ttu-id="18b30-643">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="18b30-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="18b30-644">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="18b30-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="18b30-645">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="18b30-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="18b30-646">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-646">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-647">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="18b30-648">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="18b30-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="18b30-649">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="18b30-650">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="18b30-650">For more information, see:</span></span>

* [<span data-ttu-id="18b30-651">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="18b30-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="18b30-652">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="18b30-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="18b30-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="18b30-653">EnvironmentName</span></span>

<span data-ttu-id="18b30-654">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="18b30-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="18b30-655">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="18b30-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="18b30-656">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="18b30-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="18b30-657">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="18b30-657">**Key**: `environment`</span></span>  
<span data-ttu-id="18b30-658">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-658">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-659">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="18b30-659">**Default**: `Production`</span></span>  
<span data-ttu-id="18b30-660">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="18b30-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="18b30-661">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="18b30-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="18b30-662">ShutdownTimeout</span></span>

<span data-ttu-id="18b30-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="18b30-664">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="18b30-664">The default value is five seconds.</span></span>  <span data-ttu-id="18b30-665">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="18b30-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="18b30-666">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="18b30-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="18b30-667">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="18b30-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="18b30-668">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="18b30-669">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="18b30-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="18b30-670">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="18b30-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="18b30-671">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="18b30-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="18b30-672">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="18b30-672">**Type**: `int`</span></span>  
<span data-ttu-id="18b30-673">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="18b30-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="18b30-674">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="18b30-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="18b30-675">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="18b30-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="18b30-676">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="18b30-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="18b30-677">Отключение перезагрузки конфигурации приложения при изменении</span><span class="sxs-lookup"><span data-stu-id="18b30-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="18b30-678">[По умолчанию](xref:fundamentals/configuration/index#default) при изменении файла выполняется перезагрузка *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="18b30-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="18b30-679">Чтобы отключить эту функцию перезагрузки в ASP.NET Core 5.0, предварительная версия 3 или более поздняя, присвойте ключу `hostBuilder:reloadConfigOnChange` значение `false`.</span><span class="sxs-lookup"><span data-stu-id="18b30-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="18b30-680">**Ключ**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="18b30-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="18b30-681">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-682">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="18b30-682">**Default**: `true`</span></span>  
<span data-ttu-id="18b30-683">**Аргумент командной строки**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="18b30-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="18b30-684">**Переменная среды**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="18b30-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="18b30-685">Двоеточие (`:`) не работает в качестве разделителя с иерархическими ключами переменных среды на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="18b30-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="18b30-686">Дополнительную информацию см. в разделе [Переменные среды](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="18b30-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="18b30-687">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="18b30-687">Settings for web apps</span></span>

<span data-ttu-id="18b30-688">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="18b30-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="18b30-689">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="18b30-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="18b30-690">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="18b30-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="18b30-691">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="18b30-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="18b30-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="18b30-692">CaptureStartupErrors</span></span>

<span data-ttu-id="18b30-693">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="18b30-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="18b30-694">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="18b30-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="18b30-695">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="18b30-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="18b30-696">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-697">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="18b30-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="18b30-698">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="18b30-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="18b30-699">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="18b30-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="18b30-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="18b30-700">DetailedErrors</span></span>

<span data-ttu-id="18b30-701">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="18b30-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="18b30-702">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="18b30-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="18b30-703">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-704">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="18b30-704">**Default**: `false`</span></span>  
<span data-ttu-id="18b30-705">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="18b30-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="18b30-706">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="18b30-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="18b30-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="18b30-708">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="18b30-709">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="18b30-710">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="18b30-711">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="18b30-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="18b30-712">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-712">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-713">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="18b30-713">**Default**: Empty string</span></span>  
<span data-ttu-id="18b30-714">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="18b30-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="18b30-715">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="18b30-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="18b30-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="18b30-717">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="18b30-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="18b30-718">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="18b30-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="18b30-719">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-719">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-720">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="18b30-720">**Default**: Empty string</span></span>  
<span data-ttu-id="18b30-721">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="18b30-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="18b30-722">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="18b30-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="18b30-723">HTTPS_Port</span></span>

<span data-ttu-id="18b30-724">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="18b30-724">The HTTPS redirect port.</span></span> <span data-ttu-id="18b30-725">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="18b30-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="18b30-726">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="18b30-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="18b30-727">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-727">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-728">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="18b30-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="18b30-729">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="18b30-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="18b30-730">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="18b30-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="18b30-731">PreferHostingUrls</span></span>

<span data-ttu-id="18b30-732">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="18b30-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="18b30-733">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="18b30-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="18b30-734">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-735">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="18b30-735">**Default**: `true`</span></span>  
<span data-ttu-id="18b30-736">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="18b30-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="18b30-737">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="18b30-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="18b30-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="18b30-738">PreventHostingStartup</span></span>

<span data-ttu-id="18b30-739">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="18b30-740">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="18b30-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="18b30-741">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="18b30-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="18b30-742">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="18b30-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="18b30-743">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="18b30-743">**Default**: `false`</span></span>  
<span data-ttu-id="18b30-744">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="18b30-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="18b30-745">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="18b30-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="18b30-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="18b30-746">StartupAssembly</span></span>

<span data-ttu-id="18b30-747">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="18b30-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="18b30-748">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="18b30-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="18b30-749">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-749">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-750">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="18b30-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="18b30-751">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="18b30-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="18b30-752">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="18b30-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="18b30-753">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="18b30-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="18b30-754">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="18b30-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="18b30-755">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="18b30-755">URLs</span></span>

<span data-ttu-id="18b30-756">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="18b30-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="18b30-757">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="18b30-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="18b30-758">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="18b30-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="18b30-759">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="18b30-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="18b30-760">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="18b30-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="18b30-761">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="18b30-761">**Key**: `urls`</span></span>  
<span data-ttu-id="18b30-762">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-762">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-763">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="18b30-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="18b30-764">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="18b30-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="18b30-765">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="18b30-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="18b30-766">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="18b30-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="18b30-767">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="18b30-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="18b30-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-768">WebRoot</span></span>

<span data-ttu-id="18b30-769">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="18b30-770">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="18b30-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="18b30-771">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="18b30-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="18b30-772">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="18b30-772">**Type**: `string`</span></span>  
<span data-ttu-id="18b30-773">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="18b30-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="18b30-774">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="18b30-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="18b30-775">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="18b30-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="18b30-776">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="18b30-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="18b30-777">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="18b30-777">For more information, see:</span></span>

* [<span data-ttu-id="18b30-778">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="18b30-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="18b30-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="18b30-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="18b30-780">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="18b30-780">Manage the host lifetime</span></span>

<span data-ttu-id="18b30-781">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="18b30-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="18b30-782">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="18b30-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="18b30-783">Выполнить</span><span class="sxs-lookup"><span data-stu-id="18b30-783">Run</span></span>

<span data-ttu-id="18b30-784">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="18b30-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="18b30-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-785">RunAsync</span></span>

<span data-ttu-id="18b30-786">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="18b30-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-787">RunConsoleAsync</span></span>

<span data-ttu-id="18b30-788">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="18b30-789">Начало</span><span class="sxs-lookup"><span data-stu-id="18b30-789">Start</span></span>

<span data-ttu-id="18b30-790">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="18b30-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="18b30-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-791">StartAsync</span></span>

<span data-ttu-id="18b30-792">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="18b30-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="18b30-793">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="18b30-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="18b30-794">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="18b30-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="18b30-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-795">StopAsync</span></span>

<span data-ttu-id="18b30-796">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="18b30-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="18b30-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="18b30-797">WaitForShutdown</span></span>

<span data-ttu-id="18b30-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="18b30-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="18b30-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="18b30-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="18b30-800">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18b30-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="18b30-801">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="18b30-801">External control</span></span>

<span data-ttu-id="18b30-802">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="18b30-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="18b30-803">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="18b30-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
