---
title: Создание и перенаправление журнала с помощью модуля ASP.NET Core
author: rick-anderson
description: Настройте службы IIS и модуль ASP.NET Core для записи журналов и диагностических данных.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057444"
---
# <a name="log-creation-and-redirection"></a>Создание и перенаправление журнала

Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`. Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).

Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки. Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.

Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.

Не используйте журнал stdout для ведения общего журнала приложений. Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов. Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).

При создании файла журнала автоматически добавляются отметка времени и расширение файла. Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( `.log`) к последнему сегменту атрибута пути `stdoutLogFile` (обычно `stdout`) с символами подчеркивания в качестве разделителей. Если атрибут пути `stdoutLogFile` заканчивается элементом `stdout`, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя `stdout_20180205194132_1934.log`.

Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ). После запуска все дополнительные журналы удаляются.

В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`. Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`. Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.

Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.

Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Расширенные журналы диагностики

Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики. Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в `web.config`. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:

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

Все папки, указанные в пути (`logs` в приведенном выше примере), создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).

Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.

Уровни (в порядке возрастания степени детализации):

* ОШИБКА
* ПРЕДУПРЕЖДЕНИЕ
* ИНФОРМАЦИЯ
* TRACE

Расположения (допускаются несколько расположений):

* CONSOLE
* EVENTLOG
* FILE

Параметры обработчика могут быть указаны с помощью переменных среды:

* `ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки. (Значение по умолчанию: `aspnetcore-debug.log`.)
* `ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.

> [!WARNING]
> **Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы. Размер журнала не ограничен. Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.

Пример использования элемента `aspNetCore` в файле `web.config` см. в статье [Настройка модуля ASP.NET Core с помощью `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig).
