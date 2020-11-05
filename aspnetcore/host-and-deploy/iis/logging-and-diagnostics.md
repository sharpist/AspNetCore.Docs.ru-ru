---
title: Создание и перенаправление журнала с помощью модуля ASP.NET Core
author: rick-anderson
description: Настройте службы IIS и модуль ASP.NET Core для записи журналов и диагностических данных.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057444"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="723c3-103">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="723c3-103">Log creation and redirection</span></span>

<span data-ttu-id="723c3-104">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="723c3-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="723c3-105">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="723c3-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="723c3-106">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).</span><span class="sxs-lookup"><span data-stu-id="723c3-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="723c3-107">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="723c3-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="723c3-108">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="723c3-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="723c3-109">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="723c3-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="723c3-110">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="723c3-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="723c3-111">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="723c3-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="723c3-112">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="723c3-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="723c3-113">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="723c3-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="723c3-114">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( `.log`) к последнему сегменту атрибута пути `stdoutLogFile` (обычно `stdout`) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="723c3-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="723c3-115">Если атрибут пути `stdoutLogFile` заканчивается элементом `stdout`, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="723c3-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="723c3-116">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="723c3-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="723c3-117">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="723c3-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="723c3-118">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="723c3-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="723c3-119">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="723c3-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="723c3-120">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="723c3-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="723c3-121">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="723c3-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="723c3-122">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="723c3-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="723c3-123">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="723c3-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="723c3-124">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="723c3-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="723c3-125">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="723c3-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="723c3-126">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в `web.config`.</span><span class="sxs-lookup"><span data-stu-id="723c3-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="723c3-127">Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="723c3-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="723c3-128">Все папки, указанные в пути (`logs` в приведенном выше примере), создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="723c3-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="723c3-129">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).</span><span class="sxs-lookup"><span data-stu-id="723c3-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="723c3-130">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="723c3-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="723c3-131">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="723c3-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="723c3-132">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="723c3-132">ERROR</span></span>
* <span data-ttu-id="723c3-133">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="723c3-133">WARNING</span></span>
* <span data-ttu-id="723c3-134">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="723c3-134">INFO</span></span>
* <span data-ttu-id="723c3-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="723c3-135">TRACE</span></span>

<span data-ttu-id="723c3-136">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="723c3-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="723c3-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="723c3-137">CONSOLE</span></span>
* <span data-ttu-id="723c3-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="723c3-138">EVENTLOG</span></span>
* <span data-ttu-id="723c3-139">FILE</span><span class="sxs-lookup"><span data-stu-id="723c3-139">FILE</span></span>

<span data-ttu-id="723c3-140">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="723c3-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="723c3-141">`ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="723c3-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="723c3-142">(Значение по умолчанию: `aspnetcore-debug.log`.)</span><span class="sxs-lookup"><span data-stu-id="723c3-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="723c3-143">`ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="723c3-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="723c3-144">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="723c3-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="723c3-145">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="723c3-145">The size of the log isn't limited.</span></span> <span data-ttu-id="723c3-146">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="723c3-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="723c3-147">Пример использования элемента `aspNetCore` в файле `web.config` см. в статье [Настройка модуля ASP.NET Core с помощью `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="723c3-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
