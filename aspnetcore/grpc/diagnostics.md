---
title: Ведение журнала и диагностика в gRPC на платформе .NET
author: jamesnk
description: Сведения о сборе диагностики из приложения gRPC на платформе .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: bf8068375da81288f2fbfa2c1bfafe97c03c70fc
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016184"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Ведение журнала и диагностика в gRPC на платформе .NET

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

В этой статье приводятся рекомендации по сбору диагностических данных из приложения gRPC с целью устранения неполадок. Здесь рассматриваются такие темы:

* **Ведение журналов** — структурированные журналы, записываемые в [систему ведения журналов .NET Core](xref:fundamentals/logging/index). Интерфейс <xref:Microsoft.Extensions.Logging.ILogger> используется платформами приложений для записи журналов, а пользователями — для ведения собственных журналов в приложении.
* **Трассировка** — события, которые связаны с операцией, записанной с помощью `DiaganosticSource` и `Activity`. Трассировки из источника диагностических данных обычно используются для сбора данных телеметрии приложений такими библиотеками, как [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) и [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Метрики** — представление мер данных за интервалы времени, например количество запросов в секунду. Метрики создаются с помощью `EventCounter` и могут отслеживаться с помощью программы командной строки [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) или [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Logging

Службы gRPC и клиент gRPC записывают журналы с помощью [системы ведения журналов .NET Core](xref:fundamentals/logging/index). Журналы являются хорошей отправной точкой, когда необходимо отладить код приложения, выполняющийся непредвиденным образом.

### <a name="grpc-services-logging"></a>Ведение журналов служб gRPC

> [!WARNING]
> Журналы на стороне сервера могут содержать конфиденциальные сведения из приложения. **Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.

Так как службы gRPC размещаются на платформе ASP.NET Core, они используют систему ведения журналов ASP.NET Core. В конфигурации по умолчанию gRPC записывает весьма ограниченное количество сведений, но это можно настроить. Подробные сведения о настройке ведения журналов в ASP.NET Core см. в документации по [ведению журналов в ASP.NET Core](xref:fundamentals/logging/index#configuration).

gRPC добавляет журналы в категорию `Grpc`. Чтобы включить подробные журналы от gRPC, настройте префиксы `Grpc` с уровнем `Debug` в файле *appsettings.json*, добавив следующие элементы в подраздел `LogLevel` раздела `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Эту настройку также можно произвести в файле *Startup.cs* с помощью метода `ConfigureLogging`:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Если вы не используете конфигурацию на основе JSON, задайте следующее значение конфигурации в системе конфигурации:

* `Logging:LogLevel:Grpc` = `Debug`

Сведения о задании вложенных значений конфигурации см. в документации по своей системе конфигурации. Так, при использовании переменных среды вместо символа `:` применяются два символа `_` (например, `Logging__LogLevel__Grpc`).

При сборе подробных диагностических данных для приложения мы рекомендуем использовать уровень `Debug`. На уровне `Trace` создаются очень подробные диагностические данные, которые редко требуются для диагностики проблем в приложении.

#### <a name="sample-logging-output"></a>Пример выходных данных ведения журнала

Вот пример выходных данных консоли для службы gRPC на уровне `Debug`:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Доступ к журналам на стороне сервера

Способ доступа к журналам на стороне сервера зависит от среды, в которой выполняется приложение.

#### <a name="as-a-console-app"></a>Консольное приложение

В консольном приложении по умолчанию должно быть включено [средство ведения журнала консоли](xref:fundamentals/logging/index#console). Журналы gRPC будут выводиться в консоли.

#### <a name="other-environments"></a>Другие среды

Если приложение развернуто в другой среде (например, Docker, Kubernetes или службе Windows), обратитесь к статье <xref:fundamentals/logging/index> за дополнительными сведениями о настройке подходящих поставщиков ведения журнала.

### <a name="grpc-client-logging"></a>Ведение журналов клиента gRPC

> [!WARNING]
> Журналы на стороне клиента могут содержать конфиденциальные сведения из приложения. **Никогда**  не публикуйте необработанные журналы из рабочих приложений на общедоступных форумах, таких как GitHub.

Чтобы получать журналы из клиента .NET, можно задать свойство `GrpcChannelOptions.LoggerFactory` при создании канала клиента. Если служба gRPC вызывается из приложения ASP.NET Core, производство средства ведения журнала может разрешаться посредством внедрения зависимостей:

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Другой способ включить ведение журналов клиента — использовать [производство клиента gRPC](xref:grpc/clientfactory) для создания клиента. Клиент gRPC, зарегистрированный в производстве клиента и разрешенный посредством внедрения зависимостей, будет автоматически использовать настроенные для приложения параметры ведения журналов.

Если приложение не использует внедрение зависимостей, вы можете создать экземпляр `ILoggerFactory` с помощью метода [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Для доступа к этому методу добавьте в приложение пакет [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>Области журналов клиента gRPC

Клиент gRPC добавляет [область ведения журналов](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) для журналов, создаваемых во время вызова gRPC. Область включает в себя метаданные, связанные с вызовом gRPC.

* **GrpcMethodType** — тип метода gRPC. Возможными значениями являются имена из перечисления `Grpc.Core.MethodType`, например Unary.
* **GrpcUri** — относительный универсальный код ресурса (URI) метода gRPC, например /greet.Greeter/SayHellos.

#### <a name="sample-logging-output"></a>Пример выходных данных ведения журнала

Вот пример выходных данных консоли для клиента gRPC на уровне `Debug`:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Трассировка

Службы gRPC и клиент gRPC предоставляют сведения о вызовах gRPC с помощью классов [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) (источник диагностических данных) и [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity) (действие).

* .NET gRPC использует действие для представления вызова gRPC.
* События трассировки записываются в источник диагностических данных в начале и конце действия вызова gRPC.
* При трассировке не собираются сведения о том, когда в течение времени существования вызовов потоковой передачи gRPC отправляются сообщения.

### <a name="grpc-service-tracing"></a>Трассировка службы gRPC

Службы gRPC размещаются на платформе ASP.NET Core, которая сообщает события, связанные с входящими HTTP-запросами. К существующим данным диагностики HTTP-запросов, которые предоставляет платформа ASP.NET Core, добавляются метаданные, связанные с gRPC.

* Имя источника диагностических данных — `Microsoft.AspNetCore`.
* Имя действия — `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Имя метода gRPC в вызове gRPC добавляется в качестве тега с именем `grpc.method`.
  * Код состояния вызова gRPC после его завершения добавляется в качестве тега с именем `grpc.status_code`.

### <a name="grpc-client-tracing"></a>Трассировка клиента gRPC

Клиент .NET gRPC использует `HttpClient` для выполнения вызовов gRPC. Хотя `HttpClient` записывает события диагностики, клиент .NET gRPC предоставляет пользовательский источник диагностических данных, действия и события для сбора полных сведений о вызове gRPC.

* Имя источника диагностических данных — `Grpc.Net.Client`.
* Имя действия — `Grpc.Net.Client.GrpcOut`.
  * Имя метода gRPC в вызове gRPC добавляется в качестве тега с именем `grpc.method`.
  * Код состояния вызова gRPC после его завершения добавляется в качестве тега с именем `grpc.status_code`.

### <a name="collecting-tracing"></a>Сбор трассировки

Самый простой способ использовать `DiagnosticSource` — настроить в приложении библиотеку телеметрии, например [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) или [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet). Библиотека будет обрабатывать сведения о вызовах gRPC вместе с другими данными телеметрии приложения.

Результаты трассировки можно просматривать в управляемой службе, например Application Insights, либо же можно использовать собственную распределенную систему трассировки. OpenTelemetry поддерживает экспорт данных трассировки в [Jaeger](https://www.jaegertracing.io/) и [Zipkin](https://zipkin.io/).

`DiagnosticSource` может использовать события трассировки в коде с помощью `DiagnosticListener`. Сведения о прослушивании источника диагностических данных в коде см. в [руководстве пользователя DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> В настоящее время библиотеки телеметрии не собирают данные телеметрии `Grpc.Net.Client.GrpcOut`, относящиеся к gRPC. Работа над реализацией сбора этих данных трассировки библиотеками телеметрии ведется.

## <a name="metrics"></a>Метрики

Метрики — это представление мер данных за интервалы времени, например количество запросов в секунду. Метрики позволяют отслеживать общее состояние приложения. Метрики .NET gRPC создаются с помощью `EventCounter`.

### <a name="grpc-service-metrics"></a>Метрики службы gRPC

Метрики сервера gRPC формируются в источнике событий `Grpc.AspNetCore.Server`.

| Название                      | Описание                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total Calls                   |
| `current-calls`           | Текущие вызовы                 |
| `calls-failed`            | Всего вызовов с ошибками            |
| `calls-deadline-exceeded` | Всего вызовов с истекшим сроком |
| `messages-sent`           | Всего отправленных сообщений           |
| `messages-received`       | Всего полученных сообщений       |
| `calls-unimplemented`     | Всего нереализованных вызовов     |

ASP.NET Core также предоставляет собственные метрики в источнике событий `Microsoft.AspNetCore.Hosting`.

### <a name="grpc-client-metrics"></a>Метрики клиента gRPC

Метрики клиента gRPC формируются в источнике событий `Grpc.Net.Client`.

| Название                      | Описание                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total Calls                   |
| `current-calls`           | Текущие вызовы                 |
| `calls-failed`            | Всего вызовов с ошибками            |
| `calls-deadline-exceeded` | Всего вызовов с истекшим сроком |
| `messages-sent`           | Всего отправленных сообщений           |
| `messages-received`       | Всего полученных сообщений       |

### <a name="observe-metrics"></a>Отслеживание метрик

[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) — это средство мониторинга производительности и первого уровня анализа производительности. Для отслеживания приложения .NET используйте имя поставщика `Grpc.AspNetCore.Server` или `Grpc.Net.Client`.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Еще один способ отслеживания метрик gRPC заключается в сборе данных счетчиков с помощью [пакета Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) Application Insights. После настройки Application Insights собирает показания стандартных счетчиков .NET во время выполнения. Показания счетчиков gRPC по умолчанию не собираются, но в Application Insights можно [включить дополнительные счетчики](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Счетчики gRPC, показания которых собираются Application Insights, указываются в файле *Startup.cs*:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
