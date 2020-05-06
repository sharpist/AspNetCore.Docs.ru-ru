---
title: Средства диагностики производительности
author: mjrousos
description: Полезные средства для диагностики проблем с производительностью в ASP.NET Core приложениях.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 82c724ec647dfe5547db775ebaf8c2479bb258bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775860"
---
# <a name="performance-diagnostic-tools"></a>Средства диагностики производительности

Автор: [Майк Роусос (Mike Rousos)](https://github.com/mjrousos)

В этой статье перечислены средства для диагностики проблем с производительностью в ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio Средства диагностики

[Средства профилирования и диагностики](/visualstudio/profiling) , встроенные в Visual Studio, являются хорошим местом для изучения проблем с производительностью. Эти средства являются мощными и удобными для использования в среде разработки Visual Studio. Инструментарий позволяет проводить анализ загрузки ЦП, использования памяти и событий производительности в ASP.NET Core приложениях. Встроенные средства упрощают профилирование во время разработки.

Дополнительные сведения см. в [документации по Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) предоставляет подробные данные о производительности приложения. Application Insights автоматически собирает данные о скорости ответа, частоте сбоев, времени отклика зависимости и многом другое. Application Insights поддерживает ведение журнала пользовательских событий и метрик, относящихся к вашему приложению.

Azure Application Insights предоставляет несколько способов получения аналитических сведений о наблюдаемых приложениях.

- [Схема приложений](/azure/application-insights/app-insights-app-map) — помогает выявить узкие места производительности или критические участки сбоя во всех компонентах распределенных приложений.
- [Обозреватель метрик Azure](/azure/azure-monitor/platform/metrics-getting-started) — это компонент портал Microsoft Azure, который позволяет строить диаграммы, визуально сопоставлять тенденции и исследовать пиковые значения и DIP в метриках.
- [Колонка "производительность" на портале Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Показывает сведения о производительности для различных операций в отслеживаемом приложении.
  - Позволяет детализировать одну операцию, чтобы проверить все части или зависимости, которые повлияют на длительное время.
  - Профилировщик можно вызвать здесь, чтобы получить трассировку производительности по требованию.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) позволяет регулярно и по запросу выполнять профилирование приложений .NET.  Портал Azure показывает захваченные трассировки производительности с стеками вызовов и горячими путями. Файлы трассировки также можно скачать для более глубокого анализа с помощью PerfView.

Application Insights можно использовать в различных средах:

- Оптимизация для работы в Azure.
- Работает в рабочей среде, разработке и промежуточном развертывании.
- Работает локально из [Visual Studio](/azure/application-insights/app-insights-visual-studio) или в других средах размещения.

Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) — это средство анализа производительности, создаваемое группой .NET специально для диагностики проблем производительности .NET. PerfView позволяет проводить анализ загрузки ЦП, поведения памяти и GC, событий производительности и времени стены.

Вы можете узнать больше о PerfView и о том, как приступить к работе с видеоматериалами [PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) , либо прочитать руководство пользователя, доступное в средстве или [на сайте GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) состоит из двух компонентов: средство записи производительности Windows (ЗВЧ) и анализатор производительности Windows (WPA). Средства предоставляют подробные профили производительности операционных систем и приложений Windows. WPT предоставляет более широкие возможности визуализации данных, но их сбор данных менее мощный, чем PerfView.

## <a name="perfcollect"></a>перфколлект

Хотя PerfView — это полезное средство анализа производительности для сценариев .NET, оно работает только в Windows, поэтому его нельзя использовать для получения трассировок из ASP.NET Core приложений, работающих в средах Linux.

[Перфколлект](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) — это скрипт Bash, использующий собственные средства профилирования Linux ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) и [LTTng](https://lttng.org/)) для получения трассировок в Linux, которые можно проанализировать с помощью PerfView. Перфколлект полезен в тех случаях, когда проблемы производительности отображаются в средах Linux, где PerfView нельзя использовать напрямую. Вместо этого Перфколлект может получать трассировки из приложений .NET Core, которые затем анализируются на компьютере Windows с помощью PerfView.

Дополнительные сведения о том, как установить и приступить к работе с Перфколлект, можно найти [на сайте GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Другие сторонние средства обеспечения производительности

Ниже перечислены некоторые сторонние средства обеспечения производительности, которые полезны при расследовании производительности приложений .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- Доттраце и Дотмемори из JetBrains
- VTune от Intel
