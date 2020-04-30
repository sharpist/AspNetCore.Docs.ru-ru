---
title: Конфигурация SignalR ASP.NET Core
author: bradygaster
description: Узнайте, как настроить ASP.NET Core SignalR приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558996"
---
# <a name="aspnet-core-signalr-configuration"></a>Конфигурация SignalR для ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`. `AddJsonProtocol` Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft. JSON

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки концентраторов SignalR.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |
| `MinimumProtocolVersion` | 0 | Укажите минимальную версию протокола Negotiate. Используется для ограничения клиентов на более новые версии. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).

Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder` Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Клиент Java SignalR пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) . При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание: |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `ClientCertificates` | Пусто | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Пусто | Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом. |
| `Credentials` | Пусто | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `skipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withCredentials` | `true` | Указывает, будут ли учетные данные отправляться с запросом CORS. Служба приложений Azure использует файлы cookie для закрепленных сеансов и требует, чтобы этот параметр был включен правильно. Дополнительные сведения об CORS с помощью SignalR см. в <xref:signalr/security#cross-origin-resource-sharing>разделе. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`. `AddJsonProtocol` Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft. JSON

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки концентраторов SignalR.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |
| `MinimumProtocolVersion` | 0 | Укажите минимальную версию протокола Negotiate. Используется для ограничения клиентов на более новые версии. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).

Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder` Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Клиент Java SignalR пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) . При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание: |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `ClientCertificates` | Пусто | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Пусто | Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом. |
| `Credentials` | Пусто | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `skipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices`. `AddJsonProtocol` Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> это объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации System. Text. JSON](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft. JSON

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются `System.Text.Json`в, см. раздел [Переключение на Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки концентраторов SignalR.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке ведения журнала SignalR в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы указываете для `configureLogging` установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).

Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в. `HttpHubConnectionBuilder` Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Клиент Java SignalR пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) . При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание: |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `ClientCertificates` | Пусто | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Пусто | Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом. |
| `Credentials` | Пусто | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `skipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе. `AddJsonProtocol` Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является объектом JSON.NET `JsonSerializerSettings` , который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки концентраторов SignalR.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который является буфером сервера. Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые используются в буфере сервера. Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).

Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) . При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание: |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `ClientCertificates` | Пусто | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Пусто | Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом. |
| `Credentials` | Пусто | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `skipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [аддсигналр](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` методе. `AddJsonProtocol` Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является объектом JSON.NET `JsonSerializerSettings` , который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Чтобы `Microsoft.Extensions.DependencyInjection` разрешить метод расширения, необходимо импортировать пространство имен:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки концентраторов SignalR.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `serverTimeoutInMilliseconds` параметра на клиенте измените `ServerTimeout` / параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`значение равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false`, так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в. `Startup.ConfigureServices`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Параметры одного концентратора переопределяют глобальные параметры, предоставленные `AddSignalR` в, и могут быть <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>настроены с помощью:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

В следующей таблице описаны параметры для настройки расширенных HTTP-параметров SignalR ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который является буфером сервера. Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые используются в буфере сервера. Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить `LongPolling` с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он содержит `HttpHubConnectionBuilder` параметры конфигурации построителя, а также `HubConnection` сам подкласс.

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET `ConfigureLogging` с помощью метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите пакет `Microsoft.Extensions.Logging.Console` NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить ведение журнала, `signalR.LogLevel.None` укажите в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [документации по диагностике SignalR](xref:signalr/diagnostics).

Клиент, использующий SignalR Java, использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как `java.util.logging` использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые SignalR, можно настроить в `WithUrl` вызове (`withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт событий, отправленных сервером, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки `transport` поля для объекта Options, указанного в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с запросами SignalR, `AccessTokenProvider` используйте параметр`accessTokenFactory` (в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP ( `Authorization` с использованием заголовка с типом `Bearer`). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, при отправке сервером событий и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение `access_token`строки запроса.

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

В клиенте SignalR Java можно настроить токен носителя, который будет использоваться для проверки подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [\<однострочного>](https://reactivex.io/documentation/single.html) [рксжава](https://github.com/ReactiveX/RxJava) . При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для самого `HubConnection` объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие (`onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие (`onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

В клиенте .NET значения времени ожидания задаются `TimeSpan` в виде значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, чтобы разрешить поступление проверки связи. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание: |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, `KeepAliveInterval` двойное значение сервера, которое позволяет получить время для проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [Спецификация протокола концентратора SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в методе `WithUrl` (`withUrl` в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах `HttpHubConnectionBuilder` конфигурации в клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание: |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `ClientCertificates` | Пусто | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Пусто | Коллекция файлов cookie HTTP для отправки с каждым HTTP-запросом. |
| `Credentials` | Пусто | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые необходимо сохранить из предоставленного обработчика. в противном случае настроенные параметры (такие как файлы cookie и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `skipNegotiation` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр `true` в значение, чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Пусто | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного `WithUrl`для:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
