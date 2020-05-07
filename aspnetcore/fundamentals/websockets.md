---
title: Поддержка WebSockets в ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с WebSocket в ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: da713f22582cf17f60a4deda1b689662a4e4ae06
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775444"
---
# <a name="websockets-support-in-aspnet-core"></a>Поддержка WebSockets в ASP.NET Core

Авторы: [Tom Dykstra](https://github.com/tdykstra) (Том Дикстра) и [Andrew Stanton-Nurse](https://github.com/anurse) (Эндрю Стэнтон-Нёрс)

Эта статья описывает начало работы с WebSocket в ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) — это протокол, предоставляющий сохраняемые двусторонние каналы связи по TCP-подключениям. Он используется в приложениях, где нужна быстрая связь в режиме реального времени, например в чатах, панелях мониторинга и играх.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([описание скачивания](xref:index#how-to-download-a-sample)). [Процедура выполнения](#sample-app).

## <a name="signalr"></a>SignalR

[ASP.NET Core SignalR](xref:signalr/introduction) — это библиотека, которая упрощает добавление веб-функций в приложения в режиме реального времени. Она использует WebSocket, когда это возможно.

Для большинства приложений рекомендуется использовать SignalR вместо прямых соединений WebSocket. Служба SignalR обеспечивает резервный транспорт для сред, в которых соединения WebSocket недоступны. Она также предоставляет простую модель приложений на основе удаленного вызова процедур. В большинстве сценариев SignalR по производительности не уступает прямым соединениям WebSocket.

## <a name="prerequisites"></a>Предварительные требования

* ASP.NET Core 1.1 или более поздней версии
* Любая ОС с поддержкой ASP.NET Core:
  
  * Windows 7/Windows Server 2008 или более поздних версий
  * Linux
  * macOS
  
* Если приложение выполняется в Windows с IIS:

  * Windows 8/Windows Server 2012 или более поздних версий
  * IIS 8/IIS 8 Express
  * Необходимо включить WebSockets (см. раздел [Поддержка IIS и IIS Express](#iisiis-express-support)).
  
* Если приложение выполняется в [HTTP.sys](xref:fundamentals/servers/httpsys):

  * Windows 8/Windows Server 2012 или более поздних версий

* Список поддерживаемых обозревателей см. на странице https://caniuse.com/#feat=websockets.

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>Пакет NuGet

Установка пакета [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).

::: moniker-end

## <a name="configure-the-middleware"></a>Настройка ПО промежуточного слоя


Добавьте ПО промежуточного слоя WebSocket в метод `Configure` класса `Startup`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Можно настроить следующие параметры:

* `KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым. Значение по умолчанию — две минуты.
* `ReceiveBufferSize` — размер буфера, используемого для получения данных. Опытные пользователи могут изменить этот параметр для настройки производительности с учетом размера данных. Значение по умолчанию — 4 КБ.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Можно настроить следующие параметры:

* `KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым. Значение по умолчанию — две минуты.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> — размер буфера, используемого для получения данных. Опытные пользователи могут изменить этот параметр для настройки производительности с учетом размера данных. Значение по умолчанию — 4 КБ.
* `AllowedOrigins` — список допустимых значений заголовка Origin для запросов WebSocket. По умолчанию разрешены все источники. Дополнительные сведения см. в разделе "Ограничения для источников WebSocket" ниже.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Принятие запросов WebSocket

На более позднем этапе жизненного цикла запроса (например, далее в методе `Configure` или методе действия) проверьте, относится ли запрос к WebSocket, и примите его.

Следующий пример взят из дальнейшей части метода `Configure`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Запрос WebSocket может поступить по любому URL-адресу, но этот пример кода принимает только запросы для `/ws`.

При использовании WebSocket вам **необходимо** поддерживать работу конвейера ПО промежуточного слоя, пока соединение активно. При попытке отправить или получить сообщение WebSocket после того, как конвейер ПО промежуточного слоя завершил работу, вы можете получить исключение такого вида:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Если вы используете фоновую службу для записи данных в WebSocket, убедитесь, что конвейер ПО промежуточного слоя продолжает работу. Это можно сделать с помощью <xref:System.Threading.Tasks.TaskCompletionSource%601>. Передайте `TaskCompletionSource` фоновой службе, а после завершения работы с WebSocket вызовите <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> с ее помощью. Затем используйте конструкцию `await` для ожидания свойства <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> во время запроса, как показано в следующем примере:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
Исключение закрытия WebSocket может также возникнуть, если метод действия возвращает управление слишком быстро. Если вы принимаете сокет в методе действия, дождитесь завершения выполнения кода, в котором используется сокет, прежде чем возвращать управление из метода действия.

Никогда не используйте `Task.Wait()`, `Task.Result` или аналогичные блокирующие вызовы для ожидания выполнения сокета, так как это может привести к серьезным проблемам потокового выполнения. Всегда используйте `await`.

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений

Метод `AcceptWebSocketAsync` обновляет TCP-соединение до соединения WebSocket и предоставляет объект [WebSocket](/dotnet/core/api/system.net.websockets.websocket). Используйте объект `WebSocket` для отправки и получения сообщений.

Приведенный выше код, который принимает запрос WebSocket, передает объект `WebSocket` в метод `Echo`. Код принимает сообщение и сразу отправляет такое же сообщение обратно. Сообщения отправляются и получаются циклически, пока клиент не закроет подключение:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Если вы принимаете подключение WebSocket до начала этого цикла, конвейер ПО промежуточного слоя завершается. После закрытия сокета конвейер развертывается. То есть запрос перестает перемещаться по конвейеру после принятия WebSocket. После завершения цикла и закрытия сокета запрос возвращается в конвейер.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Обработка отключений клиента

При отключении клиента из-за потери связи сервер не получает сведения автоматически. Сервер получает сообщение об отключении, только если клиент отправляет его. В случае потери подключения к Интернету это невозможно. Если при этом вы хотите выполнить определенное действие, установите время ожидания сигнала клиента с определенным интервалом.

Если клиент не всегда отправляет сообщения, а вы не хотите ожидать истечения времени ожидания только потому, что подключение не используется, тогда клиент может использовать таймер для отправки сообщения проверки связи каждые X секунд. Если сообщение не было получено на сервере в течение 2\*X секунд после предыдущего, завершите подключение и сообщите о том, что клиент отключен. Подождите вдвое дольше ожидаемого временного интервала, чтобы оставить дополнительное время на задержки в сети при отправке сообщения проверки связи.

## <a name="websocket-origin-restriction"></a>Ограничения для источников WebSocket

Варианты защиты, предоставляемые CORS, не применяются к WebSocket. Браузеры **не** поддерживают следующие задачи:

* выполнение предварительных запросов CORS;
* использование ограничений, указанных в заголовках `Access-Control`, при выполнении запросов WebSocket.

Однако браузеры отправляют заголовок `Origin` при выпуске запросов WebSocket. Приложения должны быть настроены для проверки этих заголовков, чтобы использовались только WebSocket из ожидаемых источников.

Если вы размещаете сервер по адресу "https://server.com", а клиент — по адресу "https://client.com", добавьте "https://client.com" в список `AllowedOrigins` подлежащих проверке WebSocket.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> Заголовок `Origin` контролируется клиентом и, как и заголовок `Referer`, может быть подделан. **Не** используйте эти заголовки в качестве механизма проверки подлинности.

::: moniker-end

## <a name="iisiis-express-support"></a>Поддержка служб IIS/IIS Express

Windows Server 2012 или более поздней версии и Windows 8 или более поздней версии с IIS и IIS Express 8 или более поздней версии поддерживают протокол WebSocket.

> [!NOTE]
> Соединения WebSockets всегда включены при использовании IIS Express.

### <a name="enabling-websockets-on-iis"></a>Включение WebSockets в службах IIS

Чтобы включить поддержку протокола WebSocket в Windows Server 2012 или более поздней версии:

> [!NOTE]
> Эти действия не требуется выполнять при использовании IIS Express

1. В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.
1. Выберите **Установка ролей или компонентов**. Выберите **Далее**.
1. Выберите подходящий сервер (по умолчанию выбирается локальный сервер). Выберите **Далее**.
1. Разверните **Веб-сервер (IIS)** в дереве **Роли**, разверните **Веб-сервер**, а затем **Разработка приложений**.
1. Выберите **протокол WebSocket**. Выберите **Далее**.
1. Если дополнительные функции не требуются, нажмите **Далее**.
1. Нажмите кнопку **Установить**.
1. По завершении установки выберите **Закрыть**, чтобы выйти из мастера.

Чтобы включить поддержку протокола WebSocket в Windows 8 или более поздней версии:

> [!NOTE]
> Эти действия не требуется выполнять при использовании IIS Express

1. Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).
1. Откройте следующие узлы: **IIS** > **Службы Интернета** > **Компоненты разработки приложений**.
1. Выберите компонент **Протокол WebSocket**. Нажмите кнопку **ОК**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Отключите WebSocket при использовании socket.io на Node.js

Если используется поддержка WebSocket в [socket.io](https://socket.io/) на [Node.js](https://nodejs.org/), отключите модуль WebSocket IIS по умолчанию с помощью элемента `webSocket` в *web.config* или *applicationHost.config*. Если не выполнить этот шаг, модуль IIS WebSocket попытается обработать соединение WebSocket, а не Node.js и приложение.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Пример приложения

[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) в этой статье — это эхо-приложение. Оно имеет веб-страницу, которая устанавливает соединения WebSocket, а сервер перенаправляет все полученные сообщения обратно клиенту. Запустите приложение из командной строки (оно не предназначено для запуска из Visual Studio с IIS Express) и перейдите по адресу http://localhost:5000. В верхнем левом углу веб-страницы отображается состояние подключения:

![Начальное состояние веб-страницы](websockets/_static/start.png)

Выберите **Connect** (Подключить), чтобы отправить запрос WebSocket на показанный URL-адрес. Введите тестовое сообщение и выберите **Send** (Отправить). После этого выберите **Close Socket** (Закрыть сокет). В разделе **Communication Log** (Журнал связи) выводится каждое выполняемое действие открытия, отправки и закрытия.

![Начальное состояние веб-страницы](websockets/_static/end.png)

