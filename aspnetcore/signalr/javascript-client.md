---
title: ASP.NET SignalR основной клиент JavaScript
author: bradygaster
description: Обзор ASP.NET клиента SignalR Core JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440861"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR основной клиент JavaScript

Автор: [Рэйчел Аппель](https://twitter.com/rachelappel) (Rachel Appel)

Библиотека клиентов Core JavaScript SignalR ASP.NET Позволяет разработчикам вызывать код концентратора на стороне сервера.

[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [(как скачать)](xref:index#how-to-download-a-sample)

## <a name="install-the-opno-locsignalr-client-package"></a>Установка SignalR клиентского пакета

Библиотека SignalR клиентов JavaScript поставляется в виде пакета [npm.](https://www.npmjs.com/) В следующих разделах описаны различные способы установки клиентской библиотеки.

### <a name="install-with-npm"></a>Установка с npm

При использовании Visual Studio выполняй следующие команды из **консоли Package Manager,** находясь в корневой папке. Для Visual Studio Code выполняйте следующие команды из **интегрированного терминала.**

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm устанавливает содержимое упаковки в *папку node_modules.\\ * Создайте новую папку с именем *сигнальный сигнальник* под папкой *wwwroot\\lib.* Копируйте файл *signalr.js* в папку *wwwroot'lib.signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm устанавливает содержимое упаковки в *папку node_modules.\\ * Создайте новую папку с именем *сигнальный сигнальник* под папкой *wwwroot\\lib.* Копируйте файл *signalr.js* в папку *wwwroot'lib.signalr.*

::: moniker-end

Ссылка SignalR на клиент `<script>` JavaScript в элементе. Пример:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Использование сети доставки контента (CDN)

Чтобы использовать клиентскую библиотеку без предварительного условия npm, перевесредните cdN-хозяйничаемую копию библиотеки клиента. Пример:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Библиотека клиентов доступна на следующих CDNs:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Установка с LibMan

[LibMan](xref:client-side/libman/index) может быть использован для установки определенных файлов библиотеки клиентов из библиотеки клиентов, хозяемых CDN. Например, только добавить в проект minified файл JavaScript. Подробную информацию об этом подходе можно узнать в [ SignalR библиотеке клиента.](xref:tutorials/signalr#add-the-signalr-client-library)

## <a name="connect-to-a-hub"></a>Подключение к концентратору

Следующий код создает и запускает соединение. Название концентратора является нечувствительным.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Соединения поперечному происхождению

Как правило, браузеры загружают соединения из того же домена, что и запрашиваемый на странице. Однако бывают случаи, когда требуется подключение к другому домену.

Чтобы предотвратить чтение вредоносным сайтом конфиденциальных данных с другого сайта, [соединения кросс-происхождения](xref:security/cors) отключены по умолчанию. Чтобы разрешить запрос на перекрестное `Startup` происхождение, включите его в классе.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Методы вызова концентратора от клиента

Клиенты JavaScript называют общедоступные методы на концентратах с помощью метода [вызова](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection.](/javascript/api/%40aspnet/signalr/hubconnection) Метод `invoke` принимает два аргумента:

* Название метода концентратора. В следующем примере имя метода `SendMessage`на концентраторе находится под названием .
* Любые аргументы, определенные в методе концентратора. В следующем примере имя `message`аргумента . В примере кода используется синтаксис функции стрелка, который поддерживается в текущих версиях всех основных браузеров, кроме Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Если вы используете SignalR службу Azure в *режиме Serverless,* вы не можете вызвать методы концентратора от клиента. Для получения дополнительной информации смотрите [ SignalR документацию службы](/azure/azure-signalr/signalr-concept-serverless-development-config).

Метод `invoke` возвращает [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Проблема `Promise` определяется значением возврата (если таковое значение имеется) при возврате метода на сервере. Если метод на сервере бросает ошибку, `Promise` отклоняется сообщение об ошибке. Используйте `then` `catch` и методы `Promise` на себе для `await` обработки этих случаях (или синтаксиса).

Метод `send` возвращает JavaScript `Promise`. Проблема `Promise` решается при отправке сообщения на сервер. Если есть ошибка отправки сообщения, `Promise` отклоняется с сообщением об ошибке. Используйте `then` `catch` и методы `Promise` на себе для `await` обработки этих случаях (или синтаксиса).

> [!NOTE]
> Использование `send` не дожидается получения сообщением сервером. Следовательно, невозможно вернуть данные или ошибки с сервера.

## <a name="call-client-methods-from-hub"></a>Вызов методов клиента из концентратора

Для получения сообщений из концентратора определите метод, используя [метод](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection`.

* Название метода клиента JavaScript. В следующем примере имя `ReceiveMessage`метода .
* Аргументы, которые концентратор переходит к методу. В следующем примере значение `message`аргумента .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Предыдущий код `connection.on` выполняется, когда код сервера вызывает его с помощью [метода SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRопределяет, какой метод клиента вызывать путем сопоставления имени метода и аргументов, определенных в `SendAsync` и `connection.on`.

> [!NOTE]
> В качестве наилучшей практики, `HubConnection` вызов `on` [метод ана-старта](/javascript/api/%40aspnet/signalr/hubconnection#start) на after . Это гарантирует, что ваши обработчики будут зарегистрированы до получения сообщений.

## <a name="error-handling-and-logging"></a>Обработка ошибок и ведение журнала

Цепочка `catch` метода до `start` конца метода для обработки ошибок на стороне клиента. Используйте `console.error` для вывода ошибок на консоль браузера.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Настройка отслеживания журналов на стороне клиента путем передачи регистратора и типа события для входа при подключении. Сообщения регистрируются с указанным уровнем журнала и выше. Доступные уровни журнала следующие:

* `signalR.LogLevel.Error`&ndash; Сообщения об ошибках. Только `Error` журналы сообщений.
* `signalR.LogLevel.Warning`&ndash; Предупреждающие сообщения о возможных ошибках. Входы `Warning`и `Error` сообщения.
* `signalR.LogLevel.Information`&ndash; Сообщения о состоянии без ошибок. Входы `Information` `Warning`, `Error` и сообщения.
* `signalR.LogLevel.Trace`&ndash; Отслеживаемые сообщения. Регистрирует все, включая данные, перевозимые между концентратором и клиентом.

Используйте метод [настройки](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) на [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) для настройки уровня журнала. Сообщения регистрируются на консоль браузера.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Повторное подключение клиентов

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Автоматическое подключение

Для клиента JavaScript SignalR можно настроить сятворно `withAutomaticReconnect` ею для автоматического повторного подключения с помощью метода на [HubConnectionBuilder.](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) Он не будет автоматически воссоединяться по умолчанию.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Без каких-либо параметров, `withAutomaticReconnect()` настраивает клиента ждать 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться каждую попытку повторного подключения, останавливаясь после четырех неудачных попыток.

Перед началом любых попыток `HubConnection` повторного `HubConnectionState.Reconnecting` подключения, будет `onreconnecting` переход в состояние и `Disconnected` запустить его обратные вызовы вместо перехода к состоянию и запуска его `onclose` обратных вызовов, как `HubConnection` без автоматического переподключения настроены. Это дает возможность предупредить пользователей о потере соединения и отключить элементы пользовательного иного пользовательного идентимного.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Если клиент успешно воссоединяется в течение `HubConnection` первых четырех `Connected` попыток, `onreconnected` будет переход обратно в состояние и запустить его обратные вызовы. Это дает возможность информировать пользователей о том, что соединение восстановлено.

Поскольку соединение выглядит совершенно новым для `connectionId` сервера, новый будет предоставлен обратный `onreconnected` вызов.

> [!WARNING]
> Параметр `onreconnected` обратного вызова будет неопределенным, если `HubConnection` он был настроен, чтобы [пропустить переговоры.](xref:signalr/configuration#configure-client-options) `connectionId`

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`не будет настраивать `HubConnection` для повторной попытки первоначальных сбоев запуска, поэтому сбои запуска должны обрабатываться вручную:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Если клиент не успешно воссоединится в течение `HubConnection` первых четырех `Disconnected` попыток, будет переход к состоянию и огонь его [закрытых](/javascript/api/%40aspnet/signalr/hubconnection#onclose) обратных вызовов. Это дает возможность информировать пользователей о том, что подключение было навсегда утеряно, и рекомендует обновить страницу:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Для настройки пользовательского числа попыток повторного подключения перед отключением или `withAutomaticReconnect` изменением времени повторного подключения принимает массив чисел, представляющих задержку в миллисекундах, чтобы подождать перед началом каждой попытки повторного подключения.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Предыдущий пример `HubConnection` настраивает, чтобы начать попытку повторного подключения сразу после потери соединения. Это также верно для конфигурации по умолчанию.

Если первая попытка повторного подключения не удается, вторая попытка повторного подключения также начнется немедленно, а не ждать 2 секунды, как это было бы в конфигурации по умолчанию.

Если вторая попытка повторного подключения не удается, третья попытка повторного подключения начнется через 10 секунд, что опять же похоже на конфигурацию по умолчанию.

Пользовательское поведение затем снова отклоняется от поведения по умолчанию, останавливаясь после третьего сбоя попытки повторного подключения вместо того, чтобы попытаться еще одну попытку повторного подключения в другой 30 секунд, как это было бы в конфигурации по умолчанию.

Если вы хотите еще больше контроля над временем и `withAutomaticReconnect` числом попыток `IRetryPolicy` автоматического повторного подключения, `nextRetryDelayInMilliseconds`принимает объект, реализующий интерфейс, который имеет один метод под названием.

`nextRetryDelayInMilliseconds`принимает один аргумент с `RetryContext`типом . Имеет `RetryContext` три `previousRetryCount`свойства: `elapsedMilliseconds` `retryReason` , и `number`которые `number` являются `Error` , а и соответственно. Перед первой попыткой повторного подключения, оба `previousRetryCount` и `elapsedMilliseconds` будет равен нулю, и `retryReason` будет ошибка, которая вызвала соединение будет потеряно. После каждой неудачной `previousRetryCount` попытки повтора, будет `elapsedMilliseconds` приравлечен один, будет обновляться, чтобы отразить количество `retryReason` времени, затрачиваемого на повторное подключение до сих пор в миллисекундах, и будет ошибка, которая вызвала последнюю попытку повторного подключения к сбою.

`nextRetryDelayInMilliseconds`необходимо вернуть либо число, представляющее количество миллисекунд, чтобы `null` ждать `HubConnection` до следующей попытки повторного подключения, либо если оно должно прекратить повторное подключение.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Кроме того, можно написать код, который будет повторно подключать клиента вручную, как показано в [Ручном воссоединении.](#manually-reconnect)

::: moniker-end

### <a name="manually-reconnect"></a>Ручное подключение

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> До 3.0 клиент SignalR JavaScript не переподключается автоматически. Вы должны написать код, который будет подключить клиента вручную.

::: moniker-end

Следующий код демонстрирует типичный ручной подход к воссоединению:

1. Функция (в данном случае `start` функция) создается для запуска соединения.
1. Вызов `start` функции в `onclose` обработчике событий соединения.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

В реальной реализации будет использоватьэксивентивеемый бэк-офф или повторить определенное количество раз, прежде чем сдаваться.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Справочник по JavaScript API](/javascript/api/?view=signalr-js-latest)
* [Учебник JavaScript](xref:tutorials/signalr)
* [Учебник WebPack и TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Концентраторы](xref:signalr/hubs)
* [клиент .NET](xref:signalr/dotnet-client)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
* [Запросы на кросс-происхождение (CORS)](xref:security/cors)
* [Безсерверная документация Службы Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
