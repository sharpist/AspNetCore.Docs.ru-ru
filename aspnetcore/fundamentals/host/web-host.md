---
title: Веб-узел ASP.NET Core
author: rick-anderson
description: Сведения о веб-узле в ASP.NET Core, который отвечает за запуск приложений и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 09383cb9067d7fdc2d7b69213b741e7ae823e9ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060018"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="443d4-103">Веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="443d4-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="443d4-104">Приложения ASP.NET Core настраивают и запускают *узел*.</span><span class="sxs-lookup"><span data-stu-id="443d4-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="443d4-105">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="443d4-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="443d4-106">Узел настраивает как минимум сервер и конвейер обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="443d4-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="443d4-107">Узел также может настроить ведение журнала, внедрение зависимостей и конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="443d4-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="443d4-108">В этой статье описывается веб-узел, доступ к которому предоставляется только для обеспечения обратной совместимости.</span><span class="sxs-lookup"><span data-stu-id="443d4-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="443d4-109">Для приложений всех типов рекомендуется использовать [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="443d4-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="443d4-110">В этой статье описывается веб-узел, который предназначен для размещения веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="443d4-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="443d4-111">Для приложений других типов используйте [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="443d4-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="443d4-112">Создание узла</span><span class="sxs-lookup"><span data-stu-id="443d4-112">Set up a host</span></span>

<span data-ttu-id="443d4-113">Создайте узел с помощью экземпляра [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="443d4-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="443d4-114">Обычно это делается в точке входа в приложение, то есть в методе `Main`.</span><span class="sxs-lookup"><span data-stu-id="443d4-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="443d4-115">В шаблонах проектов метод `Main` находится в файле *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="443d4-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="443d4-116">Обычно приложение вызывает [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), чтобы начать настройку узла:</span><span class="sxs-lookup"><span data-stu-id="443d4-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="443d4-117">Код, который вызывает `CreateDefaultBuilder`, находится в методе `CreateWebHostBuilder`, что отделяет его от кода в методе `Main`, который вызывает `Run` для объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="443d4-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="443d4-118">Такое отделение требуется, если вы используете [инструменты Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="443d4-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="443d4-119">Эти инструменты используют метод `CreateWebHostBuilder`, который они могут вызвать во время разработки для настройки узла без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="443d4-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="443d4-120">В качестве альтернативного способа можно использовать реализацию `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="443d4-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="443d4-121">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="443d4-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="443d4-122">Метод `CreateDefaultBuilder` выполняет указанные ниже задачи.</span><span class="sxs-lookup"><span data-stu-id="443d4-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="443d4-123">Настраивает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="443d4-124">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="443d4-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="443d4-125">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="443d4-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="443d4-126">Загружает [конфигурацию узла](#host-configuration-values) из:</span><span class="sxs-lookup"><span data-stu-id="443d4-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="443d4-127">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="443d4-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="443d4-128">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="443d4-128">Command-line arguments.</span></span>
* <span data-ttu-id="443d4-129">Загружает конфигурацию приложения в следующем порядке из:</span><span class="sxs-lookup"><span data-stu-id="443d4-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="443d4-130">*:::no-loc(appsettings.json):::*.</span><span class="sxs-lookup"><span data-stu-id="443d4-130">*:::no-loc(appsettings.json):::*.</span></span>
  * <span data-ttu-id="443d4-131">*appsettings.{Environment}.json* ;</span><span class="sxs-lookup"><span data-stu-id="443d4-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="443d4-132">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="443d4-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="443d4-133">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="443d4-133">Environment variables.</span></span>
  * <span data-ttu-id="443d4-134">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="443d4-134">Command-line arguments.</span></span>
* <span data-ttu-id="443d4-135">Настраивает [ведение журнала](xref:fundamentals/logging/index) для выходных данных консоли и отладки.</span><span class="sxs-lookup"><span data-stu-id="443d4-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="443d4-136">Ведение журнала включает в себя правила [фильтрации журналов](xref:fundamentals/logging/index#log-filtering), заданные в разделе конфигурации ведения журнала в файле *:::no-loc(appsettings.json):::* или *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="443d4-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="443d4-137">При работе за службами IIS с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module)`CreateDefaultBuilder` обеспечивает [интеграцию со службами IIS](xref:host-and-deploy/iis/index) для настройки базового адреса и порта приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="443d4-138">Кроме того, интеграция со службами IIS также позволяет настраивать [перехват приложением ошибок запуска](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="443d4-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="443d4-139">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="443d4-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="443d4-140">Устанавливает для [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) значение `true`, если приложение находится в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="443d4-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="443d4-141">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="443d4-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="443d4-142">Настройки, определенные `CreateDefaultBuilder`, можно переопределить и усилить с помощью [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) и других методов и методов расширения [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="443d4-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="443d4-143">Ниже приведены некоторые примеры:</span><span class="sxs-lookup"><span data-stu-id="443d4-143">A few examples follow:</span></span>

* <span data-ttu-id="443d4-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) используется для указания дополнительного объекта `IConfiguration` для приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="443d4-145">Следующий вызов `ConfigureAppConfiguration` добавляет делегат, чтобы включить конфигурацию приложения в файл *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="443d4-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="443d4-146">`ConfigureAppConfiguration` можно вызывать несколько раз.</span><span class="sxs-lookup"><span data-stu-id="443d4-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="443d4-147">Обратите внимание, что эта конфигурация не распространяется на узел (например, URL-адреса серверов или среду).</span><span class="sxs-lookup"><span data-stu-id="443d4-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="443d4-148">См. раздел [Значения конфигурации узла](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="443d4-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="443d4-149">Следующий вызов `ConfigureLogging` добавляет делегата, чтобы настроить минимальный уровень ведения журнала ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) для [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="443d4-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="443d4-150">Этот параметр переопределяет параметры в *appsettings.Development.json* (`LogLevel.Debug`) и *appsettings.Production.json* (`LogLevel.Error`), заданные `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="443d4-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="443d4-151">`ConfigureLogging` можно вызывать несколько раз.</span><span class="sxs-lookup"><span data-stu-id="443d4-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="443d4-152">При следующем вызове к `ConfigureKestrel` переопределяется значение по умолчанию [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), равное 30 000 000 байтов, установленное при настройке Kestrel методом `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="443d4-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="443d4-153">Следующий вызов [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) переопределяет значение по умолчанию [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), равное 30 000 000 байтов, установленное при настройке Kestrel методом `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="443d4-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="443d4-154">[Корень содержимого](xref:fundamentals/index#content-root) определяет, где узел ищет файлы содержимого, например файлы представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="443d4-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="443d4-155">При запуске приложения из корневой папки проекта эта папка используется в качестве корня содержимого.</span><span class="sxs-lookup"><span data-stu-id="443d4-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="443d4-156">Такое поведение по умолчанию принято в [Visual Studio](https://visualstudio.microsoft.com) и [шаблонах dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="443d4-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="443d4-157">Дополнительные сведения о конфигурации приложения см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="443d4-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="443d4-158">Помимо использования статического метода `CreateDefaultBuilder`, в ASP.NET Core 2.x поддерживается создание узла на основе [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="443d4-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="443d4-159">При настройке узла можно предоставить методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices).</span><span class="sxs-lookup"><span data-stu-id="443d4-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="443d4-160">Если используется класс `Startup`, в нем должен быть определен метод `Configure`.</span><span class="sxs-lookup"><span data-stu-id="443d4-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="443d4-161">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="443d4-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="443d4-162">Несколько вызовов `ConfigureServices` добавляются друг к другу.</span><span class="sxs-lookup"><span data-stu-id="443d4-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="443d4-163">При нескольких вызовах `Configure` или `UseStartup` в `WebHostBuilder` предыдущие параметры заменяются.</span><span class="sxs-lookup"><span data-stu-id="443d4-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="443d4-164">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="443d4-164">Host configuration values</span></span>

<span data-ttu-id="443d4-165">Для задания значений конфигурации узла класс [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) поддерживает следующие подходы:</span><span class="sxs-lookup"><span data-stu-id="443d4-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="443d4-166">конфигурация построителя узла, которая включает в себя переменные среды в формате `ASPNETCORE_{configurationKey}`,</span><span class="sxs-lookup"><span data-stu-id="443d4-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="443d4-167">Например, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="443d4-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="443d4-168">Расширения, такие как [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) и [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (см. раздел [Переопределение конфигурации](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="443d4-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="443d4-169">метод [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) и связанный ключ.</span><span class="sxs-lookup"><span data-stu-id="443d4-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="443d4-170">Значение, задаваемое с помощью `UseSetting`, всегда является строкой независимо от типа.</span><span class="sxs-lookup"><span data-stu-id="443d4-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="443d4-171">Хост использует значение, заданное последним.</span><span class="sxs-lookup"><span data-stu-id="443d4-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="443d4-172">Дополнительные сведения см. в подразделе [Переопределение конфигурации](#override-configuration) следующего раздела.</span><span class="sxs-lookup"><span data-stu-id="443d4-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="443d4-173">Ключ приложения (имя)</span><span class="sxs-lookup"><span data-stu-id="443d4-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="443d4-174">Свойство `IWebHostEnvironment.ApplicationName` задается автоматически при вызове [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) или [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="443d4-175">Значение присваивается имени сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="443d4-176">Чтобы явно задать значение, используйте [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey).</span><span class="sxs-lookup"><span data-stu-id="443d4-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="443d4-177">Свойство [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) задается автоматически при вызове [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) или [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="443d4-178">Значение присваивается имени сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="443d4-179">Чтобы явно задать значение, используйте [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey).</span><span class="sxs-lookup"><span data-stu-id="443d4-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="443d4-180">**Ключ** : applicationName</span><span class="sxs-lookup"><span data-stu-id="443d4-180">**Key** : applicationName</span></span>  
<span data-ttu-id="443d4-181">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-181">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-182">**По умолчанию** : имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-182">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="443d4-183">**Задается с помощью** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="443d4-183">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="443d4-184">**Переменная среды** : `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="443d4-184">**Environment variable** : `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="443d4-185">Перехват ошибок при загрузке</span><span class="sxs-lookup"><span data-stu-id="443d4-185">Capture Startup Errors</span></span>

<span data-ttu-id="443d4-186">Этот параметр управляет перехватом ошибок при загрузке.</span><span class="sxs-lookup"><span data-stu-id="443d4-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="443d4-187">**Ключ** : captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="443d4-187">**Key** : captureStartupErrors</span></span>  
<span data-ttu-id="443d4-188">**Тип** : *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="443d4-188">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="443d4-189">**По умолчанию** : `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="443d4-189">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="443d4-190">**Задается с помощью** : `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="443d4-190">**Set using** : `CaptureStartupErrors`</span></span>  
<span data-ttu-id="443d4-191">**Переменная среды** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="443d4-191">**Environment variable** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="443d4-192">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="443d4-193">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="443d4-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="443d4-194">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="443d4-194">Content root</span></span>

<span data-ttu-id="443d4-195">Этот параметр определяет то, где ASP.NET Core начинает искать файлы содержимого.</span><span class="sxs-lookup"><span data-stu-id="443d4-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="443d4-196">**Ключ** : contentRoot</span><span class="sxs-lookup"><span data-stu-id="443d4-196">**Key** : contentRoot</span></span>  
<span data-ttu-id="443d4-197">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-197">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-198">**По умолчанию** : папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-198">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="443d4-199">**Задается с помощью** : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="443d4-199">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="443d4-200">**Переменная среды** : `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="443d4-200">**Environment variable** : `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="443d4-201">Корневой каталог содержимого также используется в качестве базового пути для [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="443d4-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="443d4-202">Если путь к корневому каталогу содержимого не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="443d4-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="443d4-203">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="443d4-203">For more information, see:</span></span>

* [<span data-ttu-id="443d4-204">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="443d4-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="443d4-205">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="443d4-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="443d4-206">Подробные сообщения об ошибках</span><span class="sxs-lookup"><span data-stu-id="443d4-206">Detailed Errors</span></span>

<span data-ttu-id="443d4-207">Определяет, следует ли перехватывать подробные сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="443d4-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="443d4-208">**Ключ** : detailedErrors</span><span class="sxs-lookup"><span data-stu-id="443d4-208">**Key** : detailedErrors</span></span>  
<span data-ttu-id="443d4-209">**Тип** : *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="443d4-209">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="443d4-210">**Значение по умолчанию** : false</span><span class="sxs-lookup"><span data-stu-id="443d4-210">**Default** : false</span></span>  
<span data-ttu-id="443d4-211">**Задается с помощью** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="443d4-211">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="443d4-212">**Переменная среды** : `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="443d4-212">**Environment variable** : `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="443d4-213">Если этот параметр включен (или если параметр <a href="#environment">Среда</a> имеет значение `Development`), приложение перехватывает подробные исключения.</span><span class="sxs-lookup"><span data-stu-id="443d4-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="443d4-214">Среда</span><span class="sxs-lookup"><span data-stu-id="443d4-214">Environment</span></span>

<span data-ttu-id="443d4-215">Задает среду приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-215">Sets the app's environment.</span></span>

<span data-ttu-id="443d4-216">**Ключ** : environment</span><span class="sxs-lookup"><span data-stu-id="443d4-216">**Key** : environment</span></span>  
<span data-ttu-id="443d4-217">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-217">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-218">**По умолчанию** : Рабочие</span><span class="sxs-lookup"><span data-stu-id="443d4-218">**Default** : Production</span></span>  
<span data-ttu-id="443d4-219">**Задается с помощью** : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="443d4-219">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="443d4-220">**Переменная среды** : `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="443d4-220">**Environment variable** : `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="443d4-221">В качестве среды можно указать любое значение.</span><span class="sxs-lookup"><span data-stu-id="443d4-221">The environment can be set to any value.</span></span> <span data-ttu-id="443d4-222">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="443d4-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="443d4-223">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="443d4-223">Values aren't case sensitive.</span></span> <span data-ttu-id="443d4-224">По умолчанию значение параметра *Среда* считывается из переменной среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="443d4-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="443d4-225">При использовании [Visual Studio](https://visualstudio.microsoft.com) переменные среды можно задавать в файле *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="443d4-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="443d4-226">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="443d4-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="443d4-227">Начальные сборки размещения</span><span class="sxs-lookup"><span data-stu-id="443d4-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="443d4-228">Задает начальные сборки размещения для приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="443d4-229">**Ключ** : hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="443d4-229">**Key** : hostingStartupAssemblies</span></span>  
<span data-ttu-id="443d4-230">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-230">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-231">**По умолчанию** : Пустая строка</span><span class="sxs-lookup"><span data-stu-id="443d4-231">**Default** : Empty string</span></span>  
<span data-ttu-id="443d4-232">**Задается с помощью** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="443d4-232">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="443d4-233">**Переменная среды** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="443d4-233">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="443d4-234">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="443d4-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="443d4-235">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="443d4-236">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="443d4-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="443d4-237">HTTPS-порт</span><span class="sxs-lookup"><span data-stu-id="443d4-237">HTTPS Port</span></span>

<span data-ttu-id="443d4-238">Задайте порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="443d4-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="443d4-239">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="443d4-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="443d4-240">**Ключ** : https_port **Тип** : *string*
**По умолчанию** : значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="443d4-240">**Key** : https_port **Type** : *string*
**Default** : A default value isn't set.</span></span>
<span data-ttu-id="443d4-241">**Задается с помощью** : `UseSetting`
**переменной среды** : `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="443d4-241">**Set using** : `UseSetting`
**Environment variable** : `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="443d4-242">Исключаемые начальные сборки размещения</span><span class="sxs-lookup"><span data-stu-id="443d4-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="443d4-243">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="443d4-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="443d4-244">**Ключ** : hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="443d4-244">**Key** : hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="443d4-245">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-245">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-246">**По умолчанию** : Пустая строка</span><span class="sxs-lookup"><span data-stu-id="443d4-246">**Default** : Empty string</span></span>  
<span data-ttu-id="443d4-247">**Задается с помощью** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="443d4-247">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="443d4-248">**Переменная среды** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="443d4-248">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="443d4-249">Предпочитать URL-адреса размещения</span><span class="sxs-lookup"><span data-stu-id="443d4-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="443d4-250">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `WebHostBuilder`, вместо настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="443d4-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="443d4-251">**Ключ** : preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="443d4-251">**Key** : preferHostingUrls</span></span>  
<span data-ttu-id="443d4-252">**Тип** : *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="443d4-252">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="443d4-253">**Значение по умолчанию** : true</span><span class="sxs-lookup"><span data-stu-id="443d4-253">**Default** : true</span></span>  
<span data-ttu-id="443d4-254">**Задается с помощью** : `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="443d4-254">**Set using** : `PreferHostingUrls`</span></span>  
<span data-ttu-id="443d4-255">**Переменная среды** : `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="443d4-255">**Environment variable** : `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="443d4-256">Запретить запуск размещения</span><span class="sxs-lookup"><span data-stu-id="443d4-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="443d4-257">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="443d4-258">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="443d4-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="443d4-259">**Ключ** : preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="443d4-259">**Key** : preventHostingStartup</span></span>  
<span data-ttu-id="443d4-260">**Тип** : *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="443d4-260">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="443d4-261">**Значение по умолчанию** : false</span><span class="sxs-lookup"><span data-stu-id="443d4-261">**Default** : false</span></span>  
<span data-ttu-id="443d4-262">**Задается с помощью** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="443d4-262">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="443d4-263">**Переменная среды** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="443d4-263">**Environment variable** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="443d4-264">URL-адреса сервера</span><span class="sxs-lookup"><span data-stu-id="443d4-264">Server URLs</span></span>

<span data-ttu-id="443d4-265">Задает IP-адреса или адреса узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="443d4-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="443d4-266">**Ключ** : urls</span><span class="sxs-lookup"><span data-stu-id="443d4-266">**Key** : urls</span></span>  
<span data-ttu-id="443d4-267">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-267">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-268">**По умолчанию** : http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="443d4-268">**Default** : http://localhost:5000</span></span>  
<span data-ttu-id="443d4-269">**Задается с помощью** : `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="443d4-269">**Set using** : `UseUrls`</span></span>  
<span data-ttu-id="443d4-270">**Переменная среды** : `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="443d4-270">**Environment variable** : `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="443d4-271">Укажите разделенный точками с запятой (;) список префиксов URL-адресов, на которые сервер должен отвечать.</span><span class="sxs-lookup"><span data-stu-id="443d4-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="443d4-272">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="443d4-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="443d4-273">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="443d4-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="443d4-274">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="443d4-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="443d4-275">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="443d4-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="443d4-276">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="443d4-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="443d4-277">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="443d4-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="443d4-278">Время ожидания завершения работы</span><span class="sxs-lookup"><span data-stu-id="443d4-278">Shutdown Timeout</span></span>

<span data-ttu-id="443d4-279">Определяет, как долго необходимо ожидать завершения работы веб-узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="443d4-280">**Ключ** : shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="443d4-280">**Key** : shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="443d4-281">**Тип** : *int*</span><span class="sxs-lookup"><span data-stu-id="443d4-281">**Type** : *int*</span></span>  
<span data-ttu-id="443d4-282">**По умолчанию** : 5</span><span class="sxs-lookup"><span data-stu-id="443d4-282">**Default** : 5</span></span>  
<span data-ttu-id="443d4-283">**Задается с помощью** : `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="443d4-283">**Set using** : `UseShutdownTimeout`</span></span>  
<span data-ttu-id="443d4-284">**Переменная среды** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="443d4-284">**Environment variable** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="443d4-285">Хотя ключ принимает значение *int* с `UseSetting` (например, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), метод расширения [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) принимает [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="443d4-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="443d4-286">Во время ожидания размещение:</span><span class="sxs-lookup"><span data-stu-id="443d4-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="443d4-287">Активирует [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="443d4-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="443d4-288">Пытается остановить размещенные службы, записывая в журнал все ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="443d4-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="443d4-289">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="443d4-290">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="443d4-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="443d4-291">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="443d4-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="443d4-292">Стартовая сборка</span><span class="sxs-lookup"><span data-stu-id="443d4-292">Startup Assembly</span></span>

<span data-ttu-id="443d4-293">Определяет сборку, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="443d4-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="443d4-294">**Ключ** : startupAssembly</span><span class="sxs-lookup"><span data-stu-id="443d4-294">**Key** : startupAssembly</span></span>  
<span data-ttu-id="443d4-295">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-295">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-296">**По умолчанию** : сборка приложения</span><span class="sxs-lookup"><span data-stu-id="443d4-296">**Default** : The app's assembly</span></span>  
<span data-ttu-id="443d4-297">**Задается с помощью** : `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="443d4-297">**Set using** : `UseStartup`</span></span>  
<span data-ttu-id="443d4-298">**Переменная среды** : `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="443d4-298">**Environment variable** : `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="443d4-299">На сборку можно ссылаться по имени (`string`) или типу (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="443d4-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="443d4-300">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="443d4-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="443d4-301">Корневой веб-узел</span><span class="sxs-lookup"><span data-stu-id="443d4-301">Web root</span></span>

<span data-ttu-id="443d4-302">Задает относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="443d4-303">**Ключ** : webroot</span><span class="sxs-lookup"><span data-stu-id="443d4-303">**Key** : webroot</span></span>  
<span data-ttu-id="443d4-304">**Тип** : *string*</span><span class="sxs-lookup"><span data-stu-id="443d4-304">**Type** : *string*</span></span>  
<span data-ttu-id="443d4-305">**По умолчанию** : Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="443d4-305">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="443d4-306">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="443d4-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="443d4-307">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="443d4-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="443d4-308">**Задается с помощью** : `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="443d4-308">**Set using** : `UseWebRoot`</span></span>  
<span data-ttu-id="443d4-309">**Переменная среды** : `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="443d4-309">**Environment variable** : `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="443d4-310">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="443d4-310">For more information, see:</span></span>

* [<span data-ttu-id="443d4-311">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="443d4-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="443d4-312">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="443d4-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="443d4-313">Переопределение конфигурации</span><span class="sxs-lookup"><span data-stu-id="443d4-313">Override configuration</span></span>

<span data-ttu-id="443d4-314">Используйте [конфигурацию](xref:fundamentals/configuration/index) для настройки веб-узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="443d4-315">В приведенном ниже примере необязательная конфигурация узла задается в файле *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="443d4-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="443d4-316">Любую конфигурацию, загружаемую из файла *hostsettings.json* , можно переопределить с помощью аргументов командной строки.</span><span class="sxs-lookup"><span data-stu-id="443d4-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="443d4-317">Встроенная конфигурация (в `config`) используется для настройки узла с помощью [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="443d4-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="443d4-318">Конфигурация `IWebHostBuilder` добавляется в конфигурацию приложения, но не наоборот &mdash;`ConfigureAppConfiguration` не влияет на конфигурацию `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="443d4-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="443d4-319">Конфигурация, предоставленная методом `UseUrls`, сначала переопределяется конфигурацией из файла *hostsettings.json* , а затем с помощью аргументов командной строки:</span><span class="sxs-lookup"><span data-stu-id="443d4-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="443d4-320">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="443d4-320">*hostsettings.json* :</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="443d4-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) копирует ключи только из предоставленного объекта `IConfiguration` в конфигурацию построителя узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="443d4-322">Поэтому указание `reloadOnChange: true` для файлов JSON, XML и INI ни на что не влияет.</span><span class="sxs-lookup"><span data-stu-id="443d4-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="443d4-323">Чтобы указать узел, выполняющийся по определенному URL-адресу, можно передать нужное значение из командной строки при выполнении команды [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="443d4-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="443d4-324">Аргумент командной строки переопределяет значение `urls` из файла *hostsettings.json* , и сервер будет ожидать передачи данных через порт 8080:</span><span class="sxs-lookup"><span data-stu-id="443d4-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="443d4-325">Управление узлом</span><span class="sxs-lookup"><span data-stu-id="443d4-325">Manage the host</span></span>

<span data-ttu-id="443d4-326">**Выполнить**</span><span class="sxs-lookup"><span data-stu-id="443d4-326">**Run**</span></span>

<span data-ttu-id="443d4-327">Метод `Run` запускает веб-приложение и блокирует вызывающий поток до тех пор, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="443d4-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="443d4-328">**Запуск**</span><span class="sxs-lookup"><span data-stu-id="443d4-328">**Start**</span></span>

<span data-ttu-id="443d4-329">Чтобы запустить узел без блокировки, вызовите метод `Start`.</span><span class="sxs-lookup"><span data-stu-id="443d4-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="443d4-330">Если в метод `Start` передается список URL-адресов, он будет ожидать передачи данных по указанным URL-адресам.</span><span class="sxs-lookup"><span data-stu-id="443d4-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="443d4-331">Приложение может инициализировать и запустить новый узел с использованием предварительно настроенных значений по умолчанию `CreateDefaultBuilder` с помощью статического удобного метода.</span><span class="sxs-lookup"><span data-stu-id="443d4-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="443d4-332">Эти методы запускают сервер без вывода данных в консоль и со временем ожидания прерывания, равным [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) (Ctrl-C/SIGINT или SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="443d4-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="443d4-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="443d4-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="443d4-334">Выполните запуск с помощью `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="443d4-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-335">В браузере выполните запрос по адресу `http://localhost:5000`, чтобы получить ответ "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="443d4-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="443d4-336">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="443d4-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="443d4-337">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="443d4-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="443d4-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="443d4-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="443d4-339">Выполните запуск с помощью URL-адреса и `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="443d4-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-340">Результат будет тем же, что и при использовании **Start(RequestDelegate app)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="443d4-340">Produces the same result as **Start(RequestDelegate app)** , except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="443d4-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="443d4-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="443d4-342">Используйте экземпляр `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) для применения ПО промежуточного слоя маршрутизации:</span><span class="sxs-lookup"><span data-stu-id="443d4-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-343">В этом примере используйте следующие запросы в браузере:</span><span class="sxs-lookup"><span data-stu-id="443d4-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="443d4-344">Запрос</span><span class="sxs-lookup"><span data-stu-id="443d4-344">Request</span></span>                                    | <span data-ttu-id="443d4-345">Ответ</span><span class="sxs-lookup"><span data-stu-id="443d4-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="443d4-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="443d4-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="443d4-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="443d4-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="443d4-348">Вызывает исключение со строкой "ooops!"</span><span class="sxs-lookup"><span data-stu-id="443d4-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="443d4-349">Вызывает исключение со строкой "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="443d4-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="443d4-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="443d4-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="443d4-351">Пример "Здравствуй,</span><span class="sxs-lookup"><span data-stu-id="443d4-351">Hello World!</span></span>                             |

<span data-ttu-id="443d4-352">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="443d4-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="443d4-353">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="443d4-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="443d4-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="443d4-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="443d4-355">Используйте URL-адрес и экземпляр `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="443d4-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-356">Результат будет тем же, что и при использовании **Start(Action\<IRouteBuilder> routeBuilder)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="443d4-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)** , except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="443d4-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="443d4-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="443d4-358">Предоставьте делегат для настройки `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="443d4-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-359">В браузере выполните запрос по адресу `http://localhost:5000`, чтобы получить ответ "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="443d4-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="443d4-360">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="443d4-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="443d4-361">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="443d4-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="443d4-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="443d4-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="443d4-363">Предоставьте URL-адрес и делегат для настройки `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="443d4-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="443d4-364">Результат будет тем же, что и при использовании **StartWith(Action\<IApplicationBuilder> app)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="443d4-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)** , except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="443d4-365">Интерфейс IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="443d4-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="443d4-366">Интерфейс `IWebHostEnvironment` предоставляет сведения о среде веб-размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="443d4-367">Чтобы получить интерфейс `IWebHostEnvironment` для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="443d4-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="443d4-368">Для настройки приложения при запуске в соответствии со средой можно применять [подход на основе соглашения](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="443d4-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="443d4-369">Кроме того, можно внедрить интерфейс `IWebHostEnvironment` в конструктор `Startup` для использования в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="443d4-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="443d4-370">Помимо метода расширения `IsDevelopment`, интерфейс `IWebHostEnvironment` предоставляет методы `IsStaging`, `IsProduction` и `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="443d4-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="443d4-371">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="443d4-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="443d4-372">Службу `IWebHostEnvironment` также можно внедрять непосредственно в метод `Configure` для настройки конвейера обработки:</span><span class="sxs-lookup"><span data-stu-id="443d4-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="443d4-373">`IWebHostEnvironment` можно внедрить в метод `Invoke` при создании пользовательского [ПО промежуточного слоя](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="443d4-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="443d4-374">Интерфейс IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="443d4-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="443d4-375">[Интерфейс IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) предоставляет сведения о среде веб-размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="443d4-376">Чтобы получить интерфейс `IHostingEnvironment` для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="443d4-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="443d4-377">Для настройки приложения при запуске в соответствии со средой можно применять [подход на основе соглашения](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="443d4-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="443d4-378">Кроме того, можно внедрить интерфейс `IHostingEnvironment` в конструктор `Startup` для использования в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="443d4-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="443d4-379">Помимо метода расширения `IsDevelopment`, интерфейс `IHostingEnvironment` предоставляет методы `IsStaging`, `IsProduction` и `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="443d4-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="443d4-380">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="443d4-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="443d4-381">Службу `IHostingEnvironment` также можно внедрять непосредственно в метод `Configure` для настройки конвейера обработки:</span><span class="sxs-lookup"><span data-stu-id="443d4-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="443d4-382">`IHostingEnvironment` можно внедрить в метод `Invoke` при создании пользовательского [ПО промежуточного слоя](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="443d4-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="443d4-383">Интерфейс IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="443d4-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="443d4-384">Интерфейс `IHostApplicationLifetime` позволяет выполнять действия после запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="443d4-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="443d4-385">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов `Action`, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="443d4-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="443d4-386">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="443d4-386">Cancellation Token</span></span>    | <span data-ttu-id="443d4-387">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="443d4-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="443d4-388">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="443d4-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="443d4-389">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="443d4-390">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="443d4-390">All requests should be processed.</span></span> <span data-ttu-id="443d4-391">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="443d4-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="443d4-392">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="443d4-393">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="443d4-393">Requests may still be processing.</span></span> <span data-ttu-id="443d4-394">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="443d4-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="443d4-395">Метод `StopApplication` запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="443d4-396">Следующий класс использует `StopApplication` для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="443d4-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="443d4-397">Интерфейс IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="443d4-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="443d4-398">[Интерфейс IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) позволяет выполнять действия после запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="443d4-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="443d4-399">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов `Action`, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="443d4-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="443d4-400">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="443d4-400">Cancellation Token</span></span>    | <span data-ttu-id="443d4-401">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="443d4-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="443d4-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="443d4-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="443d4-403">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="443d4-403">The host has fully started.</span></span> |
| [<span data-ttu-id="443d4-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="443d4-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="443d4-405">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="443d4-406">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="443d4-406">All requests should be processed.</span></span> <span data-ttu-id="443d4-407">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="443d4-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="443d4-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="443d4-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="443d4-409">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="443d4-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="443d4-410">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="443d4-410">Requests may still be processing.</span></span> <span data-ttu-id="443d4-411">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="443d4-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="443d4-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="443d4-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="443d4-413">Следующий класс использует `StopApplication` для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="443d4-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="443d4-414">Проверка области</span><span class="sxs-lookup"><span data-stu-id="443d4-414">Scope validation</span></span>

<span data-ttu-id="443d4-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) устанавливает для [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) значение `true`, если приложение находится в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="443d4-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="443d4-416">Если для `ValidateScopes` установлено значение `true`, поставщик службы по умолчанию проверяет, что:</span><span class="sxs-lookup"><span data-stu-id="443d4-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="443d4-417">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="443d4-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="443d4-418">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="443d4-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="443d4-419">Корневой поставщик службы создается при вызове [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="443d4-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="443d4-420">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="443d4-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="443d4-421">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="443d4-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="443d4-422">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="443d4-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="443d4-423">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="443d4-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="443d4-424">Чтобы всегда проверять области, в том числе в рабочей среде, настройте [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) с [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) в конструкторе узлов:</span><span class="sxs-lookup"><span data-stu-id="443d4-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="443d4-425">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="443d4-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
