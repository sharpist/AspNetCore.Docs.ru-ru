---
title: Ведение журнала и диагностика в ASP.NET Core SignalR
author: anurse
description: Узнайте, как собирать диагностические сведения из SignalR приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 49029bbd53b98425a05bdb82517238e3aa2e3b1f
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506634"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>Ведение журнала и диагностика в ASP.NET Core SignalR

[Эндрю Стантон-медперсонала](https://twitter.com/anurse)

В этой статье приводятся рекомендации по сбору диагностических сведений из SignalR приложения ASP.NET Core для решения проблем.

## <a name="server-side-logging"></a>Ведение журнала на стороне сервера

> [!WARNING]
> Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения. **Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.

Поскольку SignalR является частью ASP.NET Core, используется система ведения журнала ASP.NET Core. В конфигурации по умолчанию SignalR записывает в журнал очень мало информации, но это может быть настроено. Подробные сведения о настройке ведения журналов в ASP.NET Core см. в документации по [ведению журналов в ASP.NET Core](xref:fundamentals/logging/index#configuration).

SignalR использует две категории регистратора:

* `Microsoft.AspNetCore.SignalR`: Для журналов, связанных с протоколами концентраторов, активации концентраторов, вызова методов и других действий, связанных с концентратором.
* `Microsoft.AspNetCore.Http.Connections`: Для журналов, связанных с транспортами, таких как WebSockets, длительный опрос, Server-Sent событий и низкоуровневых SignalR инфраструктуре.

Чтобы включить подробные журналы из SignalR , настройте оба предыдущих префикса на `Debug` уровень в *appsettings.json* файле, добавив следующие элементы в `LogLevel` подраздел в `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Это можно также настроить в коде `CreateWebHostBuilder` метода:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Если конфигурация на основе JSON не используется, задайте следующие значения конфигурации в системе конфигурации.

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Сведения о задании вложенных значений конфигурации см. в документации по своей системе конфигурации. Так, при использовании переменных среды вместо символа `:` применяются два символа `_` (например, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

При сборе подробных диагностических данных для приложения мы рекомендуем использовать уровень `Debug`. На уровне `Trace` создаются очень подробные диагностические данные, которые редко требуются для диагностики проблем в приложении.

## <a name="access-server-side-logs"></a>Доступ к журналам на стороне сервера

Способ доступа к журналам на стороне сервера зависит от среды, в которой выполняется приложение.

### <a name="as-a-console-app-outside-iis"></a>Как консольное приложение за пределами IIS

В консольном приложении по умолчанию должно быть включено [средство ведения журнала консоли](xref:fundamentals/logging/index#console). SignalR журналы будут отображаться в консоли.

### <a name="within-iis-express-from-visual-studio"></a>В IIS Express из Visual Studio

Visual Studio отображает выходные данные журнала в окне **вывод** . Выберите вариант раскрывающегося списка **веб-сервер ASP.NET Core** .

### <a name="azure-app-service"></a>Служба приложений Azure

Включите параметр **ведение журнала приложений (FileSystem)** в разделе **журналы диагностики** на портале службы приложений Azure и настройте **уровень** на `Verbose` . Журналы должны быть доступны в службе **потоковой передачи журналов** и в журналах файловой системы службы приложений. Дополнительные сведения см. в статье [потоковая передача журналов Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Другие среды

Если приложение развернуто в другой среде (например, Docker, Kubernetes или службе Windows), обратитесь к статье <xref:fundamentals/logging/index> за дополнительными сведениями о настройке подходящих поставщиков ведения журнала.

## <a name="javascript-client-logging"></a>Ведение журнала клиента JavaScript

> [!WARNING]
> Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения. **Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.

При использовании клиента JavaScript можно настроить параметры ведения журнала с помощью `configureLogging` метода в `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

В следующей таблице приведены уровни журнала, доступные для клиента JavaScript. Установка одного из этих значений уровня ведения журнала позволяет вести журнал на этом уровне и на всех уровнях, напревышающих его в таблице.

| Level | Описание |
| ----- | ----------- |
| `None` | Сообщения не регистрируются. |
| `Critical` | Сообщения, указывающие на сбой во всем приложении. |
| `Error` | Сообщения, указывающие на сбой в текущей операции. |
| `Warning` | Сообщения, указывающие на некритическую проблему. |
| `Information` | Информационные сообщения. |
| `Debug` | Диагностические сообщения, полезные для отладки. |
| `Trace` | Очень подробные диагностические сообщения, предназначенные для диагностики конкретных проблем. |

После настройки уровня детализации журналы записываются в консоль браузера (или стандартные выходные данные в приложении NodeJS).

Если вы хотите отправить журналы в пользовательскую систему ведения журнала, можно предоставить объект JavaScript, реализующий `ILogger` интерфейс. Единственным методом, который необходимо реализовать `log` , является, который принимает уровень события и сообщение, связанное с событием. Пример:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Ведение журнала клиента  .NET

> [!WARNING]
> Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения. **Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.

Чтобы получить журналы из клиента .NET, можно использовать `ConfigureLogging` метод для `HubConnectionBuilder` . Это работает так же, как `ConfigureLogging` и метод в `WebHostBuilder` и `HostBuilder` . Вы можете настроить те же поставщики ведения журналов, которые используются в ASP.NET Core. Однако необходимо вручную установить и включить пакеты NuGet для отдельных поставщиков ведения журнала.

Сведения о добавлении ведения журнала клиента .NET в Blazor WebAssembly приложение см <xref:blazor/fundamentals/logging#signalr-net-client-logging> . в разделе.

### <a name="console-logging"></a>Журнал консоли

Чтобы включить ведение журнала консоли, добавьте пакет [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Затем используйте `AddConsole` метод для настройки средства ведения журнала консоли:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Ведение журнала окна вывода отладки

Кроме того, можно настроить журналы для перехода в окно **вывода** в Visual Studio. Установите пакет [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) и используйте `AddDebug` метод:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Другие поставщики ведения журнала

SignalR поддерживает другие поставщики ведения журналов, такие как Serilog, SEQ, NLog или любая другая система ведения журналов, которая интегрируется с `Microsoft.Extensions.Logging` . Если ваша система ведения журналов предоставляет `ILoggerProvider` , вы можете зарегистрировать ее с помощью `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Уровень детализации элемента управления

При ведении журнала из других мест в приложении изменение уровня по умолчанию на `Debug` может оказаться слишком подробным. С помощью фильтра можно настроить уровень ведения журнала для SignalR журналов. Это можно сделать в коде во многом так же, как на сервере:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Данные трассировки сети

> [!WARNING]
> Трассировка сети содержит все содержимое каждого сообщения, отправленного приложением. **Никогда не** размещайте необработанные сетевые трассировки из рабочих приложений на общедоступные форумы, такие как GitHub.

При возникновении проблемы трассировка сети иногда может предоставить множество полезных сведений. Это особенно полезно, если вы собираетесь зафиксировать вопрос в средстве записи проблем.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Получение трассировки сети с помощью Fiddler (предпочтительный вариант)

Этот метод работает для всех приложений.

Fiddler — это очень мощный инструмент для сбора трассировок HTTP. Установите его из [Telerik.com/Fiddler](https://www.telerik.com/fiddler), запустите его, а затем запустите приложение и воспроизведите ошибку. Fiddler доступен для Windows, и существуют бета-версии для macOS и Linux.

При подключении по протоколу HTTPS необходимо выполнить некоторые дополнительные действия, чтобы гарантировать, что Fiddler может расшифровать HTTPS трафик. Дополнительные сведения см. в [документации по Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

После сбора трассировки можно экспортировать трассировку, выбрав **файл**  >  **сохранить**  >  **все сеансы** в строке меню.

![Экспорт всех сеансов из Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Получение трассировки сети с помощью tcpdump (только для macOS и Linux)

Этот метод работает для всех приложений.

Вы можете получить необработанные трассировки TCP с помощью tcpdump, выполнив следующую команду в командной оболочке. Если возникает ошибка разрешений, может потребоваться добавить или добавить к `root` команде префикс `sudo` .

```console
tcpdump -i [interface] -w trace.pcap
```

Замените на `[interface]` сетевой интерфейс, на котором вы хотите захватить. Обычно это нечто вроде `/dev/eth0` (для стандартного интерфейса Ethernet) или `/dev/lo0` (для трафика localhost). Дополнительные сведения см `tcpdump` . на справочной странице в главной системе.

## <a name="collect-a-network-trace-in-the-browser"></a>Получение трассировки сети в браузере

Этот метод работает только для приложений на основе браузера.

Большинство Средства для разработчиков браузера имеют вкладку "сеть", позволяющую записывать сетевые активности между браузером и сервером. Однако эти трассировки не включают WebSocket и Server-Sent сообщения о событиях. Если вы используете эти транспорты, лучше подходить с помощью такого средства, как Fiddler или TcpDump (описанное ниже).

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge и Internet Explorer

(Инструкции одинаковы для обеих и Internet Explorer).

1. Нажмите клавишу F12, чтобы открыть средства разработки
2. Перейдите на вкладку Сеть.
3. Обновите страницу (при необходимости) и воспроизведите проблему.
4. Щелкните значок "Сохранить" на панели инструментов, чтобы экспортировать трассировку в виде файла "HAR":

![Значок "Сохранить" на вкладке "сеть" средств разработки Microsoft ребра](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Нажмите клавишу F12, чтобы открыть средства разработки
2. Перейдите на вкладку Сеть.
3. Обновите страницу (при необходимости) и воспроизведите проблему.
4. Щелкните правой кнопкой мыши в любом месте списка запросов и выберите команду "Сохранить как HAR с содержимым":

![Параметр "Сохранить как HAR с содержимым" на вкладке "сеть" средств разработки Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Нажмите клавишу F12, чтобы открыть средства разработки
2. Перейдите на вкладку Сеть.
3. Обновите страницу (при необходимости) и воспроизведите проблему.
4. Щелкните правой кнопкой мыши в любом месте списка запросов и выберите "сохранить все как HAR".

![Параметр "сохранить все как HAR" на вкладке "сеть" средств разработки Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Присоединение файлов диагностики к проблемам GitHub

Вы можете присоединить диагностические файлы к проблемам GitHub, переименовав их, чтобы они имели `.txt` расширение, а затем перетащив их на ошибку.

> [!NOTE]
> Не вставляйте содержимое файлов журнала или трассировки сети в проблемы GitHub. Эти журналы и трассировки могут быть довольно большими, и GitHub обычно усекает их.

![Перетаскивание файлов журнала на вопрос GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Метрики

Метрики — это представление мер данных за интервалы времени. Например, количество запросов в секунду. Данные метрик позволяют выполнять наблюдение за состоянием приложения на высоком уровне. Метрики .NET gRPC создаются с помощью <xref:System.Diagnostics.Tracing.EventCounter>.

### <a name="no-locsignalr-server-metrics"></a>SignalR метрики сервера

SignalR метрики сервера указываются в <xref:Microsoft.AspNetCore.Http.Connections> источнике событий.

| Имя                    | Описание                 |
|-------------------------|-----------------------------|
| `connections-started`   | Всего запущенных подключений   |
| `connections-stopped`   | Всего остановленных подключений   |
| `connections-timed-out` | Всего подключений истекло |
| `current-connections`   | Текущие подключения         |
| `connections-duration`  | Средняя продолжительность соединения |

### <a name="observe-metrics"></a>Отслеживание метрик

[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) — это средство мониторинга производительности и первого уровня анализа производительности. Мониторинг приложения .NET с помощью в `Microsoft.AspNetCore.Http.Connections` качестве имени поставщика. Например:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
