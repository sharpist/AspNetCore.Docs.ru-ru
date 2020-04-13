---
title: конфигурация SignalR ASP.NET ядра
author: bradygaster
description: Узнайте, как настроить SignalR ASP.NET основных приложений.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223989"
---
# <a name="aspnet-core-signalr-configuration"></a>Конфигурация SignalR для ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html) Каждый протокол имеет параметры конфигурации сериализации.

Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`могут быть добавлены после `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в . Метод `AddJsonProtocol` принимает делегата, `options` который получает объект. Свойство [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) на этом `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объекте является объектом, который может быть использован для настройки последовательности аргументов и значений возврата. Для получения дополнительной [информации](/dotnet/api/system.text.json)см.

В качестве примера, чтобы настроить сериалайзер, чтобы не изменять корпус имен свойств, вместо имен "camelCase" по умолчанию, используйте следующий код в: `Startup.ConfigureServices`

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:

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

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Ньютонсофт.Джсон

Если вам нужны `Newtonsoft.Json` функции, которые `System.Text.Json`не поддерживаются в , см. [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны варианты настройки концентраторов SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале. Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано. Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым. При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора. По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию. |
| `StreamBufferCapacity` | `10` | Максимальное количество элементов, которые могут быть буферизированы для потоков загрузки клиента. Если этот предел достигнут, обработка вызовов блокируется до тех пор, пока сервер не обрабатывает элементы потока.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .

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

Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Расширенные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти. Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в .

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

В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное количество байтов, полученных от клиента, который буферизирует сервер перед применением бэкпрессии. Увеличение этого значения позволяет серверу получать большие сообщения быстрее, не применяя бэкпрессу, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора. | Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное количество байтов, отправленных приложением, которое буферизирует сервер перед наблюдением за бэкпрессионом. Увеличение этого значения позволяет серверу буферизировать большие сообщения быстрее, не ожидая избыточного давления, но может увеличить потребление памяти. |
| `Transports` | Все транспортные перевозки включены. | Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные опции, специфичные для транспорта Long Polling. |
| `WebSockets` | См. ниже. | Дополнительные опции, специфичныдля для транспорта WebSockets. |

Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса. Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса. |

Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается. |
| `SubProtocolSelector` | `null` | Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение. Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript). Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.

### <a name="configure-logging"></a>Настройка журнала

С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование. Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере. Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.

> [!NOTE]
> Для регистрации поставщиков logging необходимо установить необходимые пакеты. Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.

Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript `configureLogging` существует аналогичный метод. Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения. Входы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также предоставить `string` значение, представляющее имя уровня журнала. Это полезно при настройке регистрации SignalR в средах, где `LogLevel` у вас нет доступа к константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни журнала. Значение, которое `configureLogging` вы предоставляете для набора **минимального** уровня журнала, который будет зарегистрирован. Сообщения, зарегистрированные на этом уровне, **или уровни, перечисленные после него в таблице,** будут зарегистрированы.

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
> Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`

Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.

Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации. Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно смело игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных перевозок

Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript). Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки. Все переносы включены по умолчанию.

Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.

В клиенте Java транспорт выбирается `withTransport` с помощью метода `HttpHubConnectionBuilder`на . Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Клиент SignalR Java пока не поддерживает резервное копирование транспорта.

### <a name="configure-bearer-authentication"></a>Настройка аутентификации носителей

Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа. В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом). В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets). В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.

В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:

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

В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html) С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка тайм-аута и опций keep-alive

Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript). Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `HandshakeTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript). Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `withHandshakeResponseTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Опция .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `SkipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки запросов на аутентификации. |
| `Cookies` | Empty | Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP. |
| `Credentials` | Empty | Учетные данные для отправки с каждым запросом HTTP. |
| `CloseTimeout` | 5 с | WebSockets только. Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса. Если сервер не признает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP. Не используется для подключения WebSocket. Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра. Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр. **При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | Прокси HTTP для использования при отправке запросов HTTP. |
| `UseDefaultCredentials` | `false` | Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать аутентификацию Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который может быть использован для настройки дополнительных параметров WebSocket. Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Опция JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `skipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Вариант Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `shouldSkipNegotiate` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |

---

В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`

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

ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html) Каждый протокол имеет параметры конфигурации сериализации.

Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе. Метод `AddJsonProtocol` принимает делегата, `options` который получает объект. Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата. Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:

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

Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны варианты настройки концентраторов SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать клиента отключенным, если он не получил сообщение (включая сохранение жизни) в этом интервале. Это может занять больше времени, чем этот интервал тайм-аута для клиента на самом деле быть помечены отключены, из-за того, как это реализовано. Рекомендуемое значение в `KeepAliveInterval` два раза превышает значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым. При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора. По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию. |

Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .

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

Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Расширенные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти. Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .

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

В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное количество байтов, полученных от клиента, буфера сервера. Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти. |
| `AuthorizationData` | Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора. | Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное количество байтов, отправленных приложением, которое буферизирует сервер. Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти. |
| `Transports` | Все транспортные перевозки включены. | Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные опции, специфичные для транспорта Long Polling. |
| `WebSockets` | См. ниже. | Дополнительные опции, специфичныдля для транспорта WebSockets. |

Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса. Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса. |

Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается. |
| `SubProtocolSelector` | `null` | Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение. Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript). Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.

### <a name="configure-logging"></a>Настройка журнала

С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование. Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере. Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.

> [!NOTE]
> Для регистрации поставщиков logging необходимо установить необходимые пакеты. Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.

Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript `configureLogging` существует аналогичный метод. Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения. Входы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`

Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.

Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации. Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно смело игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных перевозок

Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript). Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки. Все переносы включены по умолчанию.

Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии веб-сокететов клиентов Java является единственным доступным транспортом.

### <a name="configure-bearer-authentication"></a>Настройка аутентификации носителей

Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа. В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом). В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets). В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.

В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:

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

В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html) С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка тайм-аута и опций keep-alive

Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript). Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `HandshakeTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript). Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `withHandshakeResponseTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которого клиент отправляет сообщения пинг. Отправка любого сообщения от клиента сбрасывает таймер в начале интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Опция .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `SkipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки запросов на аутентификации. |
| `Cookies` | Empty | Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP. |
| `Credentials` | Empty | Учетные данные для отправки с каждым запросом HTTP. |
| `CloseTimeout` | 5 с | WebSockets только. Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса. Если сервер не признает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP. Не используется для подключения WebSocket. Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра. Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр. **При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | Прокси HTTP для использования при отправке запросов HTTP. |
| `UseDefaultCredentials` | `false` | Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать аутентификацию Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который может быть использован для настройки дополнительных параметров WebSocket. Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Опция JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `skipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Вариант Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `shouldSkipNegotiate` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |

---

В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`

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

ASP.NET Core SignalR поддерживает два протокола для кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack.](https://msgpack.org/index.html) Каждый протокол имеет параметры конфигурации сериализации.

Сериализация JSON может быть настроена на сервере с помощью метода расширения [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) который может быть добавлен после [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в вашем `Startup.ConfigureServices` методе. Метод `AddJsonProtocol` принимает делегата, `options` который получает объект. Свойство [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) на этом `JsonSerializerSettings` объекте является JSON.NET объектом, который может быть использован для настройки последовательности аргументов и значений возврата. Для получения дополнительной информации смотрите [JSON.NET документацию](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
В качестве примера, чтобы настроить сериализатор, чтобы использовать имена свойств "PascalCase", вместо имен `Startup.ConfigureServices`"camelCase" по умолчанию, используйте следующий код в:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET `AddJsonProtocol` такой же метод расширения существует и на [HubConnectionBuilder.](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) Пространство `Microsoft.Extensions.DependencyInjection` имен должно быть импортировано для решения метода расширения:

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

Сериализация MessagePack может быть настроена путем предоставления делегата вызова [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Более подробную информацию можно узнать [в SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> На данный момент невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны варианты настройки концентраторов SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет первоначальное сообщение рукопожатия в течение этого промежутка времени, соединение закрывается. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение пинг отправляется автоматически, чтобы сохранить соединение открытым. При `KeepAliveInterval`изменении `ServerTimeout` / `serverTimeoutInMilliseconds` изменяйте настройку клиента. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение в `KeepAliveInterval` два раза превышает значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все протоколы, зарегистрированные на сервере, разрешены, но протоколы могут быть удалены из этого списка, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если `true`подробные сообщения об исключениях возвращаются клиентам, когда исключение выбрасывается в метод концентратора. По умолчанию, `false`поскольку эти сообщения исключений могут содержать конфиденциальную информацию. |

Параметры могут быть настроены для всех узлов, `AddSignalR` предоставляя `Startup.ConfigureServices`делегата опций для вызова в .

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

Параметры для одного концентратора `AddSignalR` переопределяют глобальные <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>параметры, предусмотренные в и могут быть настроены с использованием:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Расширенные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки расширенных настроек, связанных с транспортом и управлением буфером памяти. Эти параметры настраиваются путем передачи делегата `Startup.Configure` [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в .

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

В следующей таблице описаны варианты настройки ASP.NET расширенных опций HTTP Core SignalR:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное количество байтов, полученных от клиента, буфера сервера. Увеличение этого значения позволяет серверу получать большие сообщения, но может негативно повлиять на потребление памяти. |
| `AuthorizationData` | Данные, автоматически `Authorize` собранные из атрибутов, применяемых к классу концентратора. | Список объектов [IAuthorizeData, используемый](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) для определения того, разрешен ли клиент установить подключение к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное количество байтов, отправленных приложением, которое буферизирует сервер. Увеличение этого значения позволяет серверу отправлять большие сообщения, но может негативно повлиять на потребление памяти. |
| `Transports` | Все транспортные перевозки включены. | Немного флаги перечисляют `HttpTransportType` значения, которые могут ограничить транспорты клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные опции, специфичные для транспорта Long Polling. |
| `WebSockets` | См. ниже. | Дополнительные опции, специфичныдля для транспорта WebSockets. |

Транспорт Long Polling имеет дополнительные опции, `LongPolling` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное время ожидания сервера для отправки сообщения клиенту до прекращения одного запроса опроса. Снижение этого значения заставляет клиента чаще выдавать новые запросы опроса. |

Транспорт WebSocket имеет дополнительные опции, `WebSockets` которые могут быть настроены с помощью свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | После закрытия сервера, если клиент не закрывается в течение этого промежутка времени, соединение прекращается. |
| `SubProtocolSelector` | `null` | Делегат, который может быть `Sec-WebSocket-Protocol` использован для установки заголовка на пользовательское значение. Делегат получает значения, запрошенные клиентом в качестве входных данных, и должен вернуть желаемое значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента могут быть `HubConnectionBuilder` настроены по типу (доступно в клиентах .NET и JavaScript). Он также доступен в клиенте `HttpHubConnectionBuilder` Java, но подкласс содержит параметры конфигурации `HubConnection` builder, а также на самом.

### <a name="configure-logging"></a>Настройка журнала

С помощью метода `ConfigureLogging` в клиенте .NET настраивается журналирование. Поставщики журналов и фильтры могут быть зарегистрированы так же, как и на сервере. Для получения дополнительной информации можно ознакомиться с информацией [в ASP.NET основной](xref:fundamentals/logging/index) документации.

> [!NOTE]
> Для регистрации поставщиков logging необходимо установить необходимые пакеты. Полный список можно осмотреть [всстроенным поставщикам журнальных поставщиков](xref:fundamentals/logging/index#built-in-logging-providers) документов.

Например, чтобы включить журнал `Microsoft.Extensions.Logging.Console` консолей, установите пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript `configureLogging` существует аналогичный метод. Укажите `LogLevel` значение, указывающее минимальный уровень сообщений журнала для получения. Входы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить журнал, `signalR.LogLevel.None` укажите в методе. `configureLogging`

Для получения дополнительной [информации](xref:signalr/diagnostics)о лесозаготовках см.

Клиент SignalR Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это API для журналов высокого уровня, который позволяет пользователям библиотеки выбирать свою собственную конкретную реализацию журналов, внедряя определенную зависимость от регистрации. Следующий фрагмент кода показывает, `java.util.logging` как использовать с клиентом SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете журнал в зависимостях, SLF4J загружает регистратор без операции по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно смело игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных перевозок

Транспортировки, используемые SignalR, могут `WithUrl` быть`withUrl` настроены в вызове (в JavaScript). Бит-или значений `HttpTransportType` может быть использован для ограничения клиента использовать только указанные перевозки. Все переносы включены по умолчанию.

Например, отключить перенос событий сервера, но разрешить соединения WebSockets и Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript переносы настраиваются `transport` путем настройки поля `withUrl`на объекте опций, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Настройка аутентификации носителей

Чтобы предоставить данные аутентификации `AccessTokenProvider` вместе`accessTokenFactory` с запросами SignalR, используйте опцию (в JavaScript) для указания функции, которая возвращает нужный токен доступа. В клиенте .NET этот токен доступа передается в виде маркера HTTP `Authorization` "Проверка подлинности `Bearer`носителя" (использование заголовка с типом). В клиенте JavaScript токен доступа используется в качестве маркера Bearer, **за исключением** некоторых случаев, когда ApIs браузера ограничивают возможность применения заголовков (в частности, в запросах Server-Sent Events и WebSockets). В этих случаях токен доступа предоставляется в `access_token`качестве значения строки запроса.

В клиенте .NET `AccessTokenProvider` опция может быть указана с помощью делегата опций в: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем настройки `accessTokenFactory` поля `withUrl`на объекте опций в:

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

В клиенте SignalR Java можно настроить маркер носителя для проверки подлинности, предоставив фабрику токенов доступа к [httphubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Используйте [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для обеспечения>[RxJava](https://github.com/ReactiveX/RxJava) [\<Single String. ](https://reactivex.io/documentation/single.html) С помощью вызова [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)вы можете написать логику для создания токенов доступа для вашего клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка тайм-аута и опций keep-alive

Дополнительные опции для настройки тайм-аута и `HubConnection` поведения keep-alive доступны на самом объекте:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `Closed` сервер`onclose` отключен и запускает событие (в JavaScript). Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |
| `HandshakeTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `Closed` клиент`onclose` отменяет рукопожатие и запускает событие (в JavaScript). Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |

В клиенте .NET значения тайм-аута указаны как `TimeSpan` значения.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onclose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше, чем в сервере, чтобы время для пингов прибыло. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Тайм-аут для активности сервера. Если сервер не отправил сообщение в этом интервале, клиент считает, что `onClose` сервер отключен и запускает событие. Это значение должно быть достаточно большим для отправки с сервера **и** получения клиентом в течение интервала тайм-аута. Рекомендуемое значение — это число, по `KeepAliveInterval` крайней мере, в два раза больше значения сервера, чтобы время для пингов прибыло. |
| `withHandshakeResponseTimeout` | 15 секунд | Тайм-аут для первоначального рукопожатия сервера. Если сервер не отправляет ответ на рукопожатие в этом интервале, `onClose` клиент отменяет рукопожатие и запускает событие. Это расширенная настройка, которая должна быть изменена только в том случае, если ошибки тайм-аута рукопожатия происходят из-за серьезной задержки сети. Для получения более подробной информации [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)о процессе рукопожатия см. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры могут быть `WithUrl` `withUrl` настроены в методе (в JavaScript) `HubConnectionBuilder` `HttpHubConnectionBuilder` или на различных AIS конфигурации на клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Опция .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `SkipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки запросов на аутентификации. |
| `Cookies` | Empty | Коллекция файлов cookie HTTP для отправки с каждым запросом HTTP. |
| `Credentials` | Empty | Учетные данные для отправки с каждым запросом HTTP. |
| `CloseTimeout` | 5 с | WebSockets только. Максимальное количество времени, которое клиент ждет после закрытия сервера для подтверждения близкого запроса. Если сервер не признает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который может быть использован для `HttpMessageHandler` настройки или замены используемого для отправки запросов HTTP. Не используется для подключения WebSocket. Этот делегат должен вернуть значение без нуля, и он получает значение по умолчанию в качестве параметра. Либо изменить параметры этого значения по умолчанию и `HttpMessageHandler` вернуть его, либо вернуть новый экземпляр. **При замене обработчика убедитесь, что для копирования параметров, которые вы хотите сохранить от предоставленного обработчика, в противном случае настроенные параметры (например, cookies и заголовки) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | Прокси HTTP для использования при отправке запросов HTTP. |
| `UseDefaultCredentials` | `false` | Установите этот булейн для отправки учетных данных по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать аутентификацию Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который может быть использован для настройки дополнительных параметров WebSocket. Получает экземпляр [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) который может быть использован для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Опция JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `skipNegotiation` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Вариант Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, предоставляемую в качестве маркера проверки подлинности Bearer в запросах HTTP. |
| `shouldSkipNegotiate` | `false` | Установите `true` это, чтобы пропустить шаг переговоров. **Поддерживается только тогда, когда транспорт WebSockets является единственным включенным транспортом.** Эта настройка не может быть включена при использовании службы Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Карта дополнительных заголовков HTTP для отправки с каждым запросом HTTP. |

---

В клиенте .NET эти параметры могут быть изменены `WithUrl`делегатом опций, предоставленным:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В Клиенте JavaScript эти параметры могут быть предоставлены `withUrl`в объекте JavaScript, предоставленном:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры могут быть настроены `HttpHubConnectionBuilder` с помощью методов,`HubConnectionBuilder.create("HUB URL")`

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
