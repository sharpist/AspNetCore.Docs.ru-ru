---
title: Поддержка WebSockets в ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с WebSocket в ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057782"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="a1071-103">Поддержка WebSockets в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1071-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="a1071-104">Авторы: [Tom Dykstra](https://github.com/tdykstra) (Том Дикстра) и [Andrew Stanton-Nurse](https://github.com/anurse) (Эндрю Стэнтон-Нёрс)</span><span class="sxs-lookup"><span data-stu-id="a1071-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="a1071-105">Эта статья описывает начало работы с WebSocket в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1071-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="a1071-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) — это протокол, предоставляющий сохраняемые двусторонние каналы связи по TCP-подключениям.</span><span class="sxs-lookup"><span data-stu-id="a1071-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="a1071-107">Он используется в приложениях, где нужна быстрая связь в режиме реального времени, например в чатах, панелях мониторинга и играх.</span><span class="sxs-lookup"><span data-stu-id="a1071-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="a1071-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a1071-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="a1071-109">[Процедура выполнения](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="a1071-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="a1071-110">[ASP.NET Core SignalR](xref:signalr/introduction) — это библиотека, которая упрощает добавление веб-функций в режиме реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="a1071-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="a1071-111">Она использует WebSocket, когда это возможно.</span><span class="sxs-lookup"><span data-stu-id="a1071-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="a1071-112">Для большинства приложений рекомендуется использовать SignalR вместо прямых соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="a1071-113">Служба SignalR обеспечивает резервный транспорт для сред, в которых соединения WebSocket недоступны.</span><span class="sxs-lookup"><span data-stu-id="a1071-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="a1071-114">Она также предоставляет простую модель приложений на основе удаленного вызова процедур.</span><span class="sxs-lookup"><span data-stu-id="a1071-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="a1071-115">В большинстве сценариев SignalR по производительности не уступает прямым соединениям WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="a1071-116">Для некоторых приложений [gRPC в .NET](xref:grpc/index) предоставляет альтернативу WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a1071-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1071-117">Обязательные условия</span><span class="sxs-lookup"><span data-stu-id="a1071-117">Prerequisites</span></span>

* <span data-ttu-id="a1071-118">Любая ОС с поддержкой ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1071-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="a1071-119">Windows 7/Windows Server 2008 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="a1071-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="a1071-120">Linux</span><span class="sxs-lookup"><span data-stu-id="a1071-120">Linux</span></span>
  * <span data-ttu-id="a1071-121">macOS</span><span class="sxs-lookup"><span data-stu-id="a1071-121">macOS</span></span>  
* <span data-ttu-id="a1071-122">Если приложение выполняется в Windows с IIS:</span><span class="sxs-lookup"><span data-stu-id="a1071-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="a1071-123">Windows 8/Windows Server 2012 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="a1071-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="a1071-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="a1071-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="a1071-125">WebSockets должен быть включен.</span><span class="sxs-lookup"><span data-stu-id="a1071-125">WebSockets must be enabled.</span></span> <span data-ttu-id="a1071-126">См. раздел [Поддержка служб IIS/IIS Express](#iisiis-express-support).</span><span class="sxs-lookup"><span data-stu-id="a1071-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="a1071-127">Если приложение выполняется в [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="a1071-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="a1071-128">Windows 8/Windows Server 2012 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="a1071-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="a1071-129">Список поддерживаемых обозревателей см. на странице https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="a1071-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="a1071-130">Настройка ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="a1071-130">Configure the middleware</span></span>

<span data-ttu-id="a1071-131">Добавьте ПО промежуточного слоя WebSocket в метод `Configure` класса `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a1071-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a1071-132">Можно настроить следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="a1071-132">The following settings can be configured:</span></span>

* <span data-ttu-id="a1071-133">`KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="a1071-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="a1071-134">Значение по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="a1071-134">The default is two minutes.</span></span>
* <span data-ttu-id="a1071-135">`ReceiveBufferSize` — размер буфера, используемого для получения данных.</span><span class="sxs-lookup"><span data-stu-id="a1071-135">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="a1071-136">Опытные пользователи могут изменить этот параметр для настройки производительности с учетом размера данных.</span><span class="sxs-lookup"><span data-stu-id="a1071-136">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="a1071-137">Значение по умолчанию — 4 КБ.</span><span class="sxs-lookup"><span data-stu-id="a1071-137">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a1071-138">Можно настроить следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="a1071-138">The following settings can be configured:</span></span>

* <span data-ttu-id="a1071-139">`KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="a1071-139">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="a1071-140">Значение по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="a1071-140">The default is two minutes.</span></span>
* <span data-ttu-id="a1071-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> — размер буфера, используемого для получения данных.</span><span class="sxs-lookup"><span data-stu-id="a1071-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="a1071-142">Опытные пользователи могут изменить этот параметр для настройки производительности с учетом размера данных.</span><span class="sxs-lookup"><span data-stu-id="a1071-142">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="a1071-143">Значение по умолчанию — 4 КБ.</span><span class="sxs-lookup"><span data-stu-id="a1071-143">The default is 4 KB.</span></span>
* <span data-ttu-id="a1071-144">`AllowedOrigins` — список допустимых значений заголовка Origin для запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-144">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="a1071-145">По умолчанию разрешены все источники.</span><span class="sxs-lookup"><span data-stu-id="a1071-145">By default, all origins are allowed.</span></span> <span data-ttu-id="a1071-146">Дополнительные сведения см. в разделе "Ограничения для источников WebSocket" ниже.</span><span class="sxs-lookup"><span data-stu-id="a1071-146">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="a1071-147">Принятие запросов WebSocket</span><span class="sxs-lookup"><span data-stu-id="a1071-147">Accept WebSocket requests</span></span>

<span data-ttu-id="a1071-148">На более позднем этапе жизненного цикла запроса (например, далее в методе `Configure` или методе действия) проверьте, относится ли запрос к WebSocket, и примите его.</span><span class="sxs-lookup"><span data-stu-id="a1071-148">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="a1071-149">Следующий пример взят из дальнейшей части метода `Configure`:</span><span class="sxs-lookup"><span data-stu-id="a1071-149">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="a1071-150">Запрос WebSocket может поступить по любому URL-адресу, но этот пример кода принимает только запросы для `/ws`.</span><span class="sxs-lookup"><span data-stu-id="a1071-150">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="a1071-151">При использовании WebSocket вам **необходимо** поддерживать работу конвейера ПО промежуточного слоя, пока соединение активно.</span><span class="sxs-lookup"><span data-stu-id="a1071-151">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="a1071-152">При попытке отправить или получить сообщение WebSocket после того, как конвейер ПО промежуточного слоя завершил работу, вы можете получить исключение такого вида:</span><span class="sxs-lookup"><span data-stu-id="a1071-152">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="a1071-153">Если вы используете фоновую службу для записи данных в WebSocket, убедитесь, что конвейер ПО промежуточного слоя продолжает работу.</span><span class="sxs-lookup"><span data-stu-id="a1071-153">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="a1071-154">Это можно сделать с помощью <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="a1071-154">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="a1071-155">Передайте `TaskCompletionSource` фоновой службе, а после завершения работы с WebSocket вызовите <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> с ее помощью.</span><span class="sxs-lookup"><span data-stu-id="a1071-155">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="a1071-156">Затем используйте конструкцию `await` для ожидания свойства <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> во время запроса, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="a1071-156">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="a1071-157">Исключение закрытия WebSocket может также возникнуть, если метод действия возвращает управление слишком быстро.</span><span class="sxs-lookup"><span data-stu-id="a1071-157">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="a1071-158">При принятии сокета в методе действия дождитесь завершения выполнения кода, в котором используется сокет, прежде чем возвращать управление из метода действия.</span><span class="sxs-lookup"><span data-stu-id="a1071-158">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="a1071-159">Никогда не используйте `Task.Wait`, `Task.Result` или аналогичные блокирующие вызовы для ожидания выполнения сокета, так как это может привести к серьезным проблемам потокового выполнения.</span><span class="sxs-lookup"><span data-stu-id="a1071-159">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="a1071-160">Всегда используйте `await`.</span><span class="sxs-lookup"><span data-stu-id="a1071-160">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="a1071-161">Отправка и получение сообщений</span><span class="sxs-lookup"><span data-stu-id="a1071-161">Send and receive messages</span></span>

<span data-ttu-id="a1071-162">Метод `AcceptWebSocketAsync` обновляет TCP-соединение до соединения WebSocket и предоставляет объект [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="a1071-162">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="a1071-163">Используйте объект `WebSocket` для отправки и получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="a1071-163">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="a1071-164">Приведенный выше код, который принимает запрос WebSocket, передает объект `WebSocket` в метод `Echo`.</span><span class="sxs-lookup"><span data-stu-id="a1071-164">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="a1071-165">Код принимает сообщение и сразу отправляет такое же сообщение обратно.</span><span class="sxs-lookup"><span data-stu-id="a1071-165">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="a1071-166">Сообщения отправляются и получаются циклически, пока клиент не закроет подключение:</span><span class="sxs-lookup"><span data-stu-id="a1071-166">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="a1071-167">Если вы принимаете подключение WebSocket до начала этого цикла, конвейер ПО промежуточного слоя завершается.</span><span class="sxs-lookup"><span data-stu-id="a1071-167">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="a1071-168">После закрытия сокета конвейер развертывается.</span><span class="sxs-lookup"><span data-stu-id="a1071-168">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="a1071-169">То есть запрос перестает перемещаться по конвейеру после принятия WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-169">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="a1071-170">После завершения цикла и закрытия сокета запрос возвращается в конвейер.</span><span class="sxs-lookup"><span data-stu-id="a1071-170">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="a1071-171">Обработка отключений клиента</span><span class="sxs-lookup"><span data-stu-id="a1071-171">Handle client disconnects</span></span>

<span data-ttu-id="a1071-172">При отключении клиента из-за потери связи сервер не получает сведения автоматически.</span><span class="sxs-lookup"><span data-stu-id="a1071-172">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="a1071-173">Сервер получает сообщение об отключении, только если клиент отправляет его. В случае потери подключения к Интернету это невозможно.</span><span class="sxs-lookup"><span data-stu-id="a1071-173">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="a1071-174">Если при этом вы хотите выполнить определенное действие, установите время ожидания сигнала клиента с определенным интервалом.</span><span class="sxs-lookup"><span data-stu-id="a1071-174">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="a1071-175">Если клиент не всегда отправляет сообщения, а вы не хотите ожидать истечения времени ожидания только потому, что подключение не используется, тогда клиент может использовать таймер для отправки сообщения проверки связи каждые X секунд.</span><span class="sxs-lookup"><span data-stu-id="a1071-175">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="a1071-176">Если сообщение не было получено на сервере в течение 2\*X секунд после предыдущего, завершите подключение и сообщите о том, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="a1071-176">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="a1071-177">Подождите вдвое дольше ожидаемого временного интервала, чтобы оставить дополнительное время на задержки в сети при отправке сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="a1071-177">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="a1071-178">Ограничения для источников WebSocket</span><span class="sxs-lookup"><span data-stu-id="a1071-178">WebSocket origin restriction</span></span>

<span data-ttu-id="a1071-179">Варианты защиты, предоставляемые CORS, не применяются к WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-179">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="a1071-180">Браузеры **не** поддерживают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="a1071-180">Browsers do **not** :</span></span>

* <span data-ttu-id="a1071-181">выполнение предварительных запросов CORS;</span><span class="sxs-lookup"><span data-stu-id="a1071-181">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="a1071-182">использование ограничений, указанных в заголовках `Access-Control`, при выполнении запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-182">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="a1071-183">Однако браузеры отправляют заголовок `Origin` при выпуске запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-183">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="a1071-184">Приложения должны быть настроены для проверки этих заголовков, чтобы использовались только WebSocket из ожидаемых источников.</span><span class="sxs-lookup"><span data-stu-id="a1071-184">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="a1071-185">Если вы размещаете сервер по адресу "https://server.com", а клиент — по адресу "https://client.com", добавьте "https://client.com" в список `AllowedOrigins` подлежащих проверке WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-185">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="a1071-186">Заголовок `Origin` контролируется клиентом и, как и заголовок `Referer`, может быть подделан.</span><span class="sxs-lookup"><span data-stu-id="a1071-186">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="a1071-187">**Не** используйте эти заголовки в качестве механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a1071-187">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="a1071-188">Поддержка служб IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="a1071-188">IIS/IIS Express support</span></span>

<span data-ttu-id="a1071-189">Windows Server 2012 или более поздней версии и Windows 8 или более поздней версии с IIS и IIS Express 8 или более поздней версии поддерживают протокол WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a1071-189">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="a1071-190">Соединения WebSockets всегда включены при использовании IIS Express.</span><span class="sxs-lookup"><span data-stu-id="a1071-190">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="a1071-191">Включение WebSockets в службах IIS</span><span class="sxs-lookup"><span data-stu-id="a1071-191">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="a1071-192">Чтобы включить поддержку протокола WebSocket в Windows Server 2012 или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="a1071-192">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="a1071-193">Эти действия не требуется выполнять при использовании IIS Express</span><span class="sxs-lookup"><span data-stu-id="a1071-193">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="a1071-194">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="a1071-194">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="a1071-195">Выберите **Установка ролей или компонентов**.</span><span class="sxs-lookup"><span data-stu-id="a1071-195">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="a1071-196">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a1071-196">Select **Next**.</span></span>
1. <span data-ttu-id="a1071-197">Выберите подходящий сервер (по умолчанию выбирается локальный сервер).</span><span class="sxs-lookup"><span data-stu-id="a1071-197">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="a1071-198">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a1071-198">Select **Next**.</span></span>
1. <span data-ttu-id="a1071-199">Разверните **Веб-сервер (IIS)** в дереве **Роли** , разверните **Веб-сервер** , а затем **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="a1071-199">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server** , and then expand **Application Development**.</span></span>
1. <span data-ttu-id="a1071-200">Выберите **протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="a1071-200">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="a1071-201">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a1071-201">Select **Next**.</span></span>
1. <span data-ttu-id="a1071-202">Если дополнительные функции не требуются, нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a1071-202">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="a1071-203">Нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="a1071-203">Select **Install**.</span></span>
1. <span data-ttu-id="a1071-204">По завершении установки выберите **Закрыть** , чтобы выйти из мастера.</span><span class="sxs-lookup"><span data-stu-id="a1071-204">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="a1071-205">Чтобы включить поддержку протокола WebSocket в Windows 8 или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="a1071-205">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="a1071-206">Эти действия не требуется выполнять при использовании IIS Express</span><span class="sxs-lookup"><span data-stu-id="a1071-206">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="a1071-207">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="a1071-207">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="a1071-208">Откройте следующие узлы: **IIS** > **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="a1071-208">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="a1071-209">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="a1071-209">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a1071-210">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="a1071-210">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="a1071-211">Отключите WebSocket при использовании socket.io на Node.js</span><span class="sxs-lookup"><span data-stu-id="a1071-211">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="a1071-212">Если используется поддержка WebSocket в [socket.io](https://socket.io/) на [Node.js](https://nodejs.org/), отключите модуль WebSocket IIS по умолчанию с помощью элемента `webSocket` в *web.config* или *applicationHost.config*. Если не выполнить этот шаг, модуль IIS WebSocket попытается обработать соединение WebSocket, а не Node.js и приложение.</span><span class="sxs-lookup"><span data-stu-id="a1071-212">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="a1071-213">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="a1071-213">Sample app</span></span>

<span data-ttu-id="a1071-214">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) в этой статье — это эхо-приложение.</span><span class="sxs-lookup"><span data-stu-id="a1071-214">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="a1071-215">Оно имеет веб-страницу, которая устанавливает соединения WebSocket, а сервер перенаправляет все полученные сообщения обратно клиенту.</span><span class="sxs-lookup"><span data-stu-id="a1071-215">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="a1071-216">Запустите приложение из командной строки (оно не предназначено для запуска из Visual Studio с IIS Express) и перейдите по адресу http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="a1071-216">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="a1071-217">В верхнем левом углу веб-страницы отображается состояние подключения:</span><span class="sxs-lookup"><span data-stu-id="a1071-217">The web page shows the connection status in the upper left:</span></span>

![Начальное состояние веб-страницы](websockets/_static/start.png)

<span data-ttu-id="a1071-219">Выберите **Connect** (Подключить), чтобы отправить запрос WebSocket на показанный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="a1071-219">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="a1071-220">Введите тестовое сообщение и выберите **Send** (Отправить).</span><span class="sxs-lookup"><span data-stu-id="a1071-220">Enter a test message and select **Send**.</span></span> <span data-ttu-id="a1071-221">После этого выберите **Close Socket** (Закрыть сокет).</span><span class="sxs-lookup"><span data-stu-id="a1071-221">When done, select **Close Socket**.</span></span> <span data-ttu-id="a1071-222">В разделе **Communication Log** (Журнал связи) выводится каждое выполняемое действие открытия, отправки и закрытия.</span><span class="sxs-lookup"><span data-stu-id="a1071-222">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Начальное состояние веб-страницы](websockets/_static/end.png)

