---
title: SignalRУстранение неполадок подключения ASP.NET Core
author: bradygaster
description: SignalRУстранение неполадок подключения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059615"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="bc1e3-103">Устранение ошибок подключения</span><span class="sxs-lookup"><span data-stu-id="bc1e3-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="bc1e3-104">В этом разделе содержится справка по ошибкам, которые могут возникнуть при попытке установить подключение к SignalR концентратору ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="bc1e3-105">Код ответа 404</span><span class="sxs-lookup"><span data-stu-id="bc1e3-105">Response code 404</span></span>

<span data-ttu-id="bc1e3-106">При использовании WebSockets и `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="bc1e3-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="bc1e3-107">При использовании нескольких серверов без прикрепленных сеансов подключение можно запустить на одном сервере, а затем переключиться на другой сервер.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="bc1e3-108">Другой сервер не знает о предыдущем подключении.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="bc1e3-109">Убедитесь, что клиент подключается к правильной конечной точке.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="bc1e3-110">Например, сервер размещается в `http://127.0.0.1:5000/hub/myHub` , а клиент пытается подключиться к `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="bc1e3-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="bc1e3-111">Если соединение использует идентификатор и отправляет запрос на сервер после согласования, сервер выполняет слишком много времени.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="bc1e3-112">Удаляет идентификатор.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-112">Deletes the ID.</span></span>
  * <span data-ttu-id="bc1e3-113">Возвращает 404.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="bc1e3-114">Код ответа 400 или 503</span><span class="sxs-lookup"><span data-stu-id="bc1e3-114">Response code 400 or 503</span></span>

<span data-ttu-id="bc1e3-115">Для следующей ошибки:</span><span class="sxs-lookup"><span data-stu-id="bc1e3-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="bc1e3-116">Эта ошибка обычно вызвана тем, что клиент использует только транспорт WebSockets, но протокол WebSockets не включен на сервере.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="bc1e3-117">Код ответа 307</span><span class="sxs-lookup"><span data-stu-id="bc1e3-117">Response code 307</span></span>

<span data-ttu-id="bc1e3-118">При использовании WebSockets и `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="bc1e3-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="bc1e3-119">Эта ошибка также может произойти во время запроса на согласование.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="bc1e3-120">Распространенная причина:</span><span class="sxs-lookup"><span data-stu-id="bc1e3-120">Common cause:</span></span>
* <span data-ttu-id="bc1e3-121">Приложение настроено для принудительного применения протокола HTTPS путем вызова `UseHttpsRedirection` в `Startup` или принудительно использует HTTPS с помощью правила переопределения URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="bc1e3-122">Возможное решение:</span><span class="sxs-lookup"><span data-stu-id="bc1e3-122">Possible solution:</span></span>
* <span data-ttu-id="bc1e3-123">Измените URL-адрес на стороне клиента с "http" на "HTTPS".</span><span class="sxs-lookup"><span data-stu-id="bc1e3-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="bc1e3-124">Код ответа 405</span><span class="sxs-lookup"><span data-stu-id="bc1e3-124">Response code 405</span></span>

<span data-ttu-id="bc1e3-125">Код состояния HTTP 405 — метод не разрешен</span><span class="sxs-lookup"><span data-stu-id="bc1e3-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="bc1e3-126">В приложении не включен механизм [CORS](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="bc1e3-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="bc1e3-127">Код ответа 0</span><span class="sxs-lookup"><span data-stu-id="bc1e3-127">Response code 0</span></span>

<span data-ttu-id="bc1e3-128">Код состояния HTTP 0 — обычно возникает ошибка [CORS](xref:signalr/security#cross-origin-resource-sharing) , код состояния не указан</span><span class="sxs-lookup"><span data-stu-id="bc1e3-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="bc1e3-129">Добавление ожидаемых источников в `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="bc1e3-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="bc1e3-130">Добавьте `.AllowCredentials()` в политику CORS.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="bc1e3-131">Невозможно использовать `.AllowAnyOrigin()` или `.WithOrigins("*")` с этим параметром</span><span class="sxs-lookup"><span data-stu-id="bc1e3-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="bc1e3-132">Код ответа 413</span><span class="sxs-lookup"><span data-stu-id="bc1e3-132">Response code 413</span></span>

<span data-ttu-id="bc1e3-133">Код состояния HTTP 413-слишком большой объем полезных данных</span><span class="sxs-lookup"><span data-stu-id="bc1e3-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="bc1e3-134">Это часто бывает вызвано наличием маркера доступа размером более 4 КБ.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="bc1e3-135">При использовании службы Azure SignalR сократите размер маркера, настроив утверждения, отправляемые через службу:</span><span class="sxs-lookup"><span data-stu-id="bc1e3-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="bc1e3-136">Временные сбои сети</span><span class="sxs-lookup"><span data-stu-id="bc1e3-136">Transient network failures</span></span>

<span data-ttu-id="bc1e3-137">Временные сбои сети могут закрывать SignalR подключение.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="bc1e3-138">Сервер может интерпретировать закрытое соединение как мягкое отключение клиента.</span><span class="sxs-lookup"><span data-stu-id="bc1e3-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="bc1e3-139">Дополнительные сведения о том, почему клиент отключен в таких случаях, [собирают журналы с клиента и с сервера](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="bc1e3-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>