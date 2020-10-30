---
title: SignalRУстранение неполадок подключения ASP.NET Core
author: bradygaster
description: SignalRУстранение неполадок подключения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059615"
---
# <a name="troubleshoot-connection-errors"></a>Устранение ошибок подключения

В этом разделе содержится справка по ошибкам, которые могут возникнуть при попытке установить подключение к SignalR концентратору ASP.NET Core.

### <a name="response-code-404"></a>Код ответа 404

При использовании WebSockets и `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* При использовании нескольких серверов без прикрепленных сеансов подключение можно запустить на одном сервере, а затем переключиться на другой сервер. Другой сервер не знает о предыдущем подключении.
* Убедитесь, что клиент подключается к правильной конечной точке. Например, сервер размещается в `http://127.0.0.1:5000/hub/myHub` , а клиент пытается подключиться к `http://127.0.0.1:5000/myHub` .
* Если соединение использует идентификатор и отправляет запрос на сервер после согласования, сервер выполняет слишком много времени.

  * Удаляет идентификатор.
  * Возвращает 404.

### <a name="response-code-400-or-503"></a>Код ответа 400 или 503

Для следующей ошибки:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Эта ошибка обычно вызвана тем, что клиент использует только транспорт WebSockets, но протокол WebSockets не включен на сервере.

### <a name="response-code-307"></a>Код ответа 307

При использовании WebSockets и `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Эта ошибка также может произойти во время запроса на согласование.

Распространенная причина:
* Приложение настроено для принудительного применения протокола HTTPS путем вызова `UseHttpsRedirection` в `Startup` или принудительно использует HTTPS с помощью правила переопределения URL-адреса.

Возможное решение:
* Измените URL-адрес на стороне клиента с "http" на "HTTPS". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Код ответа 405

Код состояния HTTP 405 — метод не разрешен

* В приложении не включен механизм [CORS](xref:signalr/security#cross-origin-resource-sharing)

### <a name="response-code-0"></a>Код ответа 0

Код состояния HTTP 0 — обычно возникает ошибка [CORS](xref:signalr/security#cross-origin-resource-sharing) , код состояния не указан

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Добавление ожидаемых источников в `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Добавьте `.AllowCredentials()` в политику CORS. Невозможно использовать `.AllowAnyOrigin()` или `.WithOrigins("*")` с этим параметром

### <a name="response-code-413"></a>Код ответа 413

Код состояния HTTP 413-слишком большой объем полезных данных

Это часто бывает вызвано наличием маркера доступа размером более 4 КБ.

* При использовании службы Azure SignalR сократите размер маркера, настроив утверждения, отправляемые через службу:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Временные сбои сети

Временные сбои сети могут закрывать SignalR подключение. Сервер может интерпретировать закрытое соединение как мягкое отключение клиента. Дополнительные сведения о том, почему клиент отключен в таких случаях, [собирают журналы с клиента и с сервера](xref:signalr/diagnostics).