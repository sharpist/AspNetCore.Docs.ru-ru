---
title: Средства диагностики производительности
author: mjrousos
description: Полезные средства для диагностики проблем с производительностью в ASP.NET Core приложениях.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060863"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="e4874-103">Средства диагностики производительности</span><span class="sxs-lookup"><span data-stu-id="e4874-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="e4874-104">Автор: [Майк Роусос (Mike Rousos)](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="e4874-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="e4874-105">В этой статье перечислены средства для диагностики проблем с производительностью в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4874-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="e4874-106">Visual Studio Средства диагностики</span><span class="sxs-lookup"><span data-stu-id="e4874-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="e4874-107">[Средства профилирования и диагностики](/visualstudio/profiling) , встроенные в Visual Studio, являются хорошим местом для изучения проблем с производительностью.</span><span class="sxs-lookup"><span data-stu-id="e4874-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="e4874-108">Эти средства являются мощными и удобными для использования в среде разработки Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4874-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="e4874-109">Инструментарий позволяет проводить анализ загрузки ЦП, использования памяти и событий производительности в ASP.NET Core приложениях.</span><span class="sxs-lookup"><span data-stu-id="e4874-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="e4874-110">Встроенные средства упрощают профилирование во время разработки.</span><span class="sxs-lookup"><span data-stu-id="e4874-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="e4874-111">Дополнительные сведения см. в [документации по Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="e4874-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="e4874-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="e4874-112">Application Insights</span></span>

<span data-ttu-id="e4874-113">[Application Insights](/azure/application-insights/app-insights-overview) предоставляет подробные данные о производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="e4874-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="e4874-114">Application Insights автоматически собирает данные о скорости ответа, частоте сбоев, времени отклика зависимости и многом другое.</span><span class="sxs-lookup"><span data-stu-id="e4874-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="e4874-115">Application Insights поддерживает ведение журнала пользовательских событий и метрик, относящихся к вашему приложению.</span><span class="sxs-lookup"><span data-stu-id="e4874-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="e4874-116">Azure Application Insights предоставляет несколько способов получения аналитических сведений о наблюдаемых приложениях.</span><span class="sxs-lookup"><span data-stu-id="e4874-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="e4874-117">[Схема приложений](/azure/application-insights/app-insights-app-map) — помогает выявить узкие места производительности или критические участки сбоя во всех компонентах распределенных приложений.</span><span class="sxs-lookup"><span data-stu-id="e4874-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="e4874-118">[Обозреватель метрик Azure](/azure/azure-monitor/platform/metrics-getting-started) — это компонент портал Microsoft Azure, который позволяет строить диаграммы, визуально сопоставлять тенденции и исследовать пиковые значения и DIP в метриках.</span><span class="sxs-lookup"><span data-stu-id="e4874-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="e4874-119">[Колонка "производительность" на портале Application Insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="e4874-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="e4874-120">Показывает сведения о производительности для различных операций в отслеживаемом приложении.</span><span class="sxs-lookup"><span data-stu-id="e4874-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="e4874-121">Позволяет детализировать одну операцию, чтобы проверить все части или зависимости, которые повлияют на длительное время.</span><span class="sxs-lookup"><span data-stu-id="e4874-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="e4874-122">Профилировщик можно вызвать здесь, чтобы получить трассировку производительности по требованию.</span><span class="sxs-lookup"><span data-stu-id="e4874-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="e4874-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) позволяет регулярно и по запросу выполнять профилирование приложений .NET.</span><span class="sxs-lookup"><span data-stu-id="e4874-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="e4874-124">Портал Azure показывает захваченные трассировки производительности с стеками вызовов и горячими путями.</span><span class="sxs-lookup"><span data-stu-id="e4874-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="e4874-125">Файлы трассировки также можно скачать для более глубокого анализа с помощью PerfView.</span><span class="sxs-lookup"><span data-stu-id="e4874-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="e4874-126">Application Insights можно использовать в различных средах:</span><span class="sxs-lookup"><span data-stu-id="e4874-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="e4874-127">Оптимизация для работы в Azure.</span><span class="sxs-lookup"><span data-stu-id="e4874-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="e4874-128">Работает в рабочей среде, разработке и промежуточном развертывании.</span><span class="sxs-lookup"><span data-stu-id="e4874-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="e4874-129">Работает локально из [Visual Studio](/azure/application-insights/app-insights-visual-studio) или в других средах размещения.</span><span class="sxs-lookup"><span data-stu-id="e4874-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="e4874-130">Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e4874-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="e4874-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="e4874-131">PerfView</span></span>

<span data-ttu-id="e4874-132">[PerfView](https://github.com/Microsoft/perfview) — это средство анализа производительности, создаваемое группой .NET специально для диагностики проблем производительности .NET.</span><span class="sxs-lookup"><span data-stu-id="e4874-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="e4874-133">PerfView позволяет проводить анализ загрузки ЦП, поведения памяти и GC, событий производительности и времени стены.</span><span class="sxs-lookup"><span data-stu-id="e4874-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="e4874-134">Вы можете узнать больше о PerfView и о том, как приступить к работе с видеоматериалами [PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) , либо прочитать руководство пользователя, доступное в средстве или [на сайте GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="e4874-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="e4874-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="e4874-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="e4874-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) состоит из двух компонентов: средство записи производительности Windows (ЗВЧ) и анализатор производительности Windows (WPA).</span><span class="sxs-lookup"><span data-stu-id="e4874-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="e4874-137">Средства предоставляют подробные профили производительности операционных систем и приложений Windows.</span><span class="sxs-lookup"><span data-stu-id="e4874-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="e4874-138">WPT предоставляет более широкие возможности визуализации данных, но их сбор данных менее мощный, чем PerfView.</span><span class="sxs-lookup"><span data-stu-id="e4874-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="e4874-139">перфколлект</span><span class="sxs-lookup"><span data-stu-id="e4874-139">PerfCollect</span></span>

<span data-ttu-id="e4874-140">Хотя PerfView — это полезное средство анализа производительности для сценариев .NET, оно работает только в Windows, поэтому его нельзя использовать для получения трассировок из ASP.NET Core приложений, работающих в средах Linux.</span><span class="sxs-lookup"><span data-stu-id="e4874-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="e4874-141">[Перфколлект](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) — это скрипт Bash, использующий собственные средства профилирования Linux ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) и [LTTng](https://lttng.org/)) для получения трассировок в Linux, которые можно проанализировать с помощью PerfView.</span><span class="sxs-lookup"><span data-stu-id="e4874-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="e4874-142">Перфколлект полезен в тех случаях, когда проблемы производительности отображаются в средах Linux, где PerfView нельзя использовать напрямую.</span><span class="sxs-lookup"><span data-stu-id="e4874-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="e4874-143">Вместо этого Перфколлект может получать трассировки из приложений .NET Core, которые затем анализируются на компьютере Windows с помощью PerfView.</span><span class="sxs-lookup"><span data-stu-id="e4874-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="e4874-144">Дополнительные сведения о том, как установить и приступить к работе с Перфколлект, можно найти [на сайте GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="e4874-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="e4874-145">Другие сторонние средства обеспечения производительности</span><span class="sxs-lookup"><span data-stu-id="e4874-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="e4874-146">Ниже перечислены некоторые сторонние средства обеспечения производительности, которые полезны при расследовании производительности приложений .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4874-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="e4874-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="e4874-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="e4874-148">[доттраце](https://www.jetbrains.com/profiler/) и [дотмемори](https://www.jetbrains.com/dotmemory/) из [JetBrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="e4874-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="e4874-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) от Intel</span><span class="sxs-lookup"><span data-stu-id="e4874-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
