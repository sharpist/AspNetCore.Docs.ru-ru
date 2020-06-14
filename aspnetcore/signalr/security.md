---
title: Вопросы безопасности в ASP.NET CoreSignalR
author: bradygaster
description: Узнайте, как использовать проверку подлинности и авторизацию в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/security
ms.openlocfilehash: b80ece8c74c0f1d4d8518f0da16a91db9687336c
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755891"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="e85a1-103">Вопросы безопасности в ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="e85a1-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e85a1-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e85a1-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="e85a1-105">В этой статье приводятся сведения о защите SignalR .</span><span class="sxs-lookup"><span data-stu-id="e85a1-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="e85a1-106">Предоставление общего доступа к ресурсам независимо от источника</span><span class="sxs-lookup"><span data-stu-id="e85a1-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="e85a1-107">[Общий доступ к ресурсам между источниками (CORS)](https://www.w3.org/TR/cors/) можно использовать, чтобы разрешить подключения между источниками SignalR в браузере.</span><span class="sxs-lookup"><span data-stu-id="e85a1-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="e85a1-108">Если код JavaScript размещается в другом домене из SignalR приложения, необходимо включить по [промежуточного слоя CORS](xref:security/cors) , чтобы разрешить JavaScript подключаться к SignalR приложению.</span><span class="sxs-lookup"><span data-stu-id="e85a1-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="e85a1-109">Разрешать запросы между источниками только из доменов, которым вы доверяете или контролируете.</span><span class="sxs-lookup"><span data-stu-id="e85a1-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="e85a1-110">Пример:</span><span class="sxs-lookup"><span data-stu-id="e85a1-110">For example:</span></span>

* <span data-ttu-id="e85a1-111">Ваш сайт размещен на`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="e85a1-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="e85a1-112">Ваше SignalR приложение размещено на`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="e85a1-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="e85a1-113">CORS следует настроить в приложении, SignalR чтобы разрешить только источник `www.example.com` .</span><span class="sxs-lookup"><span data-stu-id="e85a1-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="e85a1-114">Дополнительные сведения о настройке CORS см. в разделе [Включение запросов между источниками (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="e85a1-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="e85a1-115">**требуются** следующие политики CORS:</span><span class="sxs-lookup"><span data-stu-id="e85a1-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="e85a1-116">Разрешить конкретные ожидаемые источники.</span><span class="sxs-lookup"><span data-stu-id="e85a1-116">Allow the specific expected origins.</span></span> <span data-ttu-id="e85a1-117">Разрешение любого источника возможно, но **не** является безопасным или рекомендуемым.</span><span class="sxs-lookup"><span data-stu-id="e85a1-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="e85a1-118">Методы HTTP `GET` и `POST` должны быть разрешены.</span><span class="sxs-lookup"><span data-stu-id="e85a1-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="e85a1-119">Учетные данные должны быть разрешены для правильной работы прикрепленных сеансов на основе файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="e85a1-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="e85a1-120">Они должны быть включены, даже если проверка подлинности не используется.</span><span class="sxs-lookup"><span data-stu-id="e85a1-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e85a1-121">Однако в 5,0 мы предоставили в клиенте TypeScript возможность не использовать учетные данные.</span><span class="sxs-lookup"><span data-stu-id="e85a1-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="e85a1-122">Параметр не использовать учетные данные следует использовать только в том случае, если известно 100%, что учетные данные, такие как файлы cookie, не требуются в приложении (файлы cookie используются службой приложений Azure при использовании нескольких серверов для прикрепленных сеансов).</span><span class="sxs-lookup"><span data-stu-id="e85a1-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="e85a1-123">Например, следующая политика CORS позволяет SignalR клиенту, размещенному в браузере, `https://example.com` получать доступ к SignalR приложению, размещенному на `https://signalr.example.com` :</span><span class="sxs-lookup"><span data-stu-id="e85a1-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="e85a1-124">Ограничение источника WebSocket</span><span class="sxs-lookup"><span data-stu-id="e85a1-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e85a1-125">Варианты защиты, предоставляемые CORS, не применяются к WebSocket.</span><span class="sxs-lookup"><span data-stu-id="e85a1-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="e85a1-126">Ограничение [по источнику](xref:fundamentals/websockets#websocket-origin-restriction)для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="e85a1-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e85a1-127">Варианты защиты, предоставляемые CORS, не применяются к WebSocket.</span><span class="sxs-lookup"><span data-stu-id="e85a1-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="e85a1-128">Браузеры **не** поддерживают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="e85a1-128">Browsers do **not**:</span></span>

* <span data-ttu-id="e85a1-129">выполнение предварительных запросов CORS;</span><span class="sxs-lookup"><span data-stu-id="e85a1-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="e85a1-130">использование ограничений, указанных в заголовках `Access-Control`, при выполнении запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="e85a1-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="e85a1-131">Однако браузеры отправляют заголовок `Origin` при выпуске запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="e85a1-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="e85a1-132">Приложения должны быть настроены для проверки этих заголовков, чтобы использовались только WebSocket из ожидаемых источников.</span><span class="sxs-lookup"><span data-stu-id="e85a1-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="e85a1-133">В ASP.NET Core 2,1 и более поздних версиях проверка заголовков может быть достигнута с помощью пользовательского промежуточного слоя, размещенного **до `UseSignalR` , и по промежуточного слоя** в `Configure` :</span><span class="sxs-lookup"><span data-stu-id="e85a1-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="e85a1-134">Заголовок `Origin` контролируется клиентом и, как и заголовок `Referer`, может быть подделан.</span><span class="sxs-lookup"><span data-stu-id="e85a1-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="e85a1-135">Эти заголовки **не** следует использовать в качестве механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e85a1-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="e85a1-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="e85a1-136">ConnectionId</span></span>

<span data-ttu-id="e85a1-137">Предоставление доступа `ConnectionId` может привести к злонамеренному олицетворению, если SignalR версия сервера или клиента ASP.NET Core 2,2 или более ранняя.</span><span class="sxs-lookup"><span data-stu-id="e85a1-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="e85a1-138">Если SignalR версия сервера и клиента ASP.NET Core 3,0 или более поздней версии, `ConnectionToken` вместо `ConnectionId` необходимо хранить секретный код.</span><span class="sxs-lookup"><span data-stu-id="e85a1-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="e85a1-139">`ConnectionToken`Цель не предоставляется ни в одном API.</span><span class="sxs-lookup"><span data-stu-id="e85a1-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="e85a1-140">Может быть трудно убедиться, что старые SignalR клиенты не подключаются к серверу, поэтому, даже если SignalR серверная версия ASP.NET Core 3,0 или более поздней версии, она не `ConnectionId` должна быть предоставлена.</span><span class="sxs-lookup"><span data-stu-id="e85a1-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="e85a1-141">Ведение журнала маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="e85a1-141">Access token logging</span></span>

<span data-ttu-id="e85a1-142">При использовании веб-сокетов или событий, отправленных сервером, клиент браузера отправляет маркер доступа в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="e85a1-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="e85a1-143">Получение маркера доступа через строку запроса, как правило, безопасно с использованием стандартного `Authorization` заголовка.</span><span class="sxs-lookup"><span data-stu-id="e85a1-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="e85a1-144">Всегда используйте протокол HTTPS, чтобы обеспечить безопасное сквозное подключение между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="e85a1-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="e85a1-145">Многие веб-серверы заключают в журнал URL-адрес каждого запроса, включая строку запроса.</span><span class="sxs-lookup"><span data-stu-id="e85a1-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="e85a1-146">Ведение журнала URL-адресов может регистрировать маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="e85a1-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="e85a1-147">ASP.NET Core записывает URL-адрес для каждого запроса по умолчанию, который будет включать строку запроса.</span><span class="sxs-lookup"><span data-stu-id="e85a1-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="e85a1-148">Пример:</span><span class="sxs-lookup"><span data-stu-id="e85a1-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="e85a1-149">Если у вас есть вопросы о записи этих данных в журналы сервера, можно полностью отключить это ведение журнала, настроив `Microsoft.AspNetCore.Hosting` средство ведения журнала на `Warning` уровень или выше (эти сообщения записываются на `Info` уровне).</span><span class="sxs-lookup"><span data-stu-id="e85a1-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="e85a1-150">Дополнительные сведения см. в разделе [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) для получения дополнительных сведений.</span><span class="sxs-lookup"><span data-stu-id="e85a1-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="e85a1-151">Если вы по-прежнему хотите заносить в журнал определенные сведения о запросе, можно [написать по промежуточного слоя](xref:fundamentals/middleware/write) для записи нужных данных и отфильтровать `access_token` значение строки запроса (при наличии).</span><span class="sxs-lookup"><span data-stu-id="e85a1-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="e85a1-152">Исключения</span><span class="sxs-lookup"><span data-stu-id="e85a1-152">Exceptions</span></span>

<span data-ttu-id="e85a1-153">Сообщения об исключениях, как правило, считаются конфиденциальными данными, которые не следует раскрывать клиенту.</span><span class="sxs-lookup"><span data-stu-id="e85a1-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="e85a1-154">По умолчанию SignalR не отправляет сведения об исключении, созданном методом концентратора клиенту.</span><span class="sxs-lookup"><span data-stu-id="e85a1-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="e85a1-155">Вместо этого клиент получает общее сообщение с указанием произошедшей ошибки.</span><span class="sxs-lookup"><span data-stu-id="e85a1-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="e85a1-156">Доставка сообщений об исключениях клиенту может быть переопределена (например, в разработке или тестировании) с помощью [енабледетаиледеррорс](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="e85a1-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="e85a1-157">Сообщения об исключениях не должны предоставляться клиенту в рабочих приложениях.</span><span class="sxs-lookup"><span data-stu-id="e85a1-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="e85a1-158">Управление буферами</span><span class="sxs-lookup"><span data-stu-id="e85a1-158">Buffer management</span></span>

SignalR<span data-ttu-id="e85a1-159">использует буферы каждого подключения для управления входящими и исходящими сообщениями.</span><span class="sxs-lookup"><span data-stu-id="e85a1-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="e85a1-160">По умолчанию SignalR ограничивает эти буферы до 32 КБ.</span><span class="sxs-lookup"><span data-stu-id="e85a1-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="e85a1-161">Максимальное сообщение, которое клиент или сервер может отправить, — 32 КБ.</span><span class="sxs-lookup"><span data-stu-id="e85a1-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="e85a1-162">Максимальный объем памяти, потребляемый соединением для сообщений, составляет 32 КБ.</span><span class="sxs-lookup"><span data-stu-id="e85a1-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="e85a1-163">Если размер сообщений всегда меньше 32 КБ, можно уменьшить ограничение, которое:</span><span class="sxs-lookup"><span data-stu-id="e85a1-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="e85a1-164">Запрещает клиенту отправить сообщение большего размера.</span><span class="sxs-lookup"><span data-stu-id="e85a1-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="e85a1-165">Серверу не придется распределять большие буферы для приема сообщений.</span><span class="sxs-lookup"><span data-stu-id="e85a1-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="e85a1-166">Если размер сообщений превышает 32 КБ, можно увеличить это ограничение.</span><span class="sxs-lookup"><span data-stu-id="e85a1-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="e85a1-167">Увеличение этого предела означает:</span><span class="sxs-lookup"><span data-stu-id="e85a1-167">Increasing this limit means:</span></span>

* <span data-ttu-id="e85a1-168">Клиент может привести к выделению сервером больших буферов памяти.</span><span class="sxs-lookup"><span data-stu-id="e85a1-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="e85a1-169">Выделение сервером больших буферов может сократить число одновременных подключений.</span><span class="sxs-lookup"><span data-stu-id="e85a1-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="e85a1-170">Существуют ограничения для входящих и исходящих сообщений. они могут быть настроены для объекта [хттпконнектиондиспатчероптионс](xref:signalr/configuration#configure-server-options) , настроенного в `MapHub` :</span><span class="sxs-lookup"><span data-stu-id="e85a1-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="e85a1-171">`ApplicationMaxBufferSize`представляет максимальное число байтов от клиента, на котором находятся буферы сервера.</span><span class="sxs-lookup"><span data-stu-id="e85a1-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="e85a1-172">Если клиент пытается отправить сообщение, размер которого превышает это ограничение, соединение может быть закрыто.</span><span class="sxs-lookup"><span data-stu-id="e85a1-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="e85a1-173">`TransportMaxBufferSize`представляет максимальное число байтов, которое может быть отправлено сервером.</span><span class="sxs-lookup"><span data-stu-id="e85a1-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="e85a1-174">Если сервер пытается отправить сообщение (включая возвращаемые значения из методов концентратора), превышающие это ограничение, будет создано исключение.</span><span class="sxs-lookup"><span data-stu-id="e85a1-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="e85a1-175">Установка ограничения `0` отключает ограничение.</span><span class="sxs-lookup"><span data-stu-id="e85a1-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="e85a1-176">Удаление ограничения позволяет клиенту отправить сообщение любого размера.</span><span class="sxs-lookup"><span data-stu-id="e85a1-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="e85a1-177">Вредоносные клиенты, отправляющие большие сообщения, могут вызвать чрезмерное выделение памяти.</span><span class="sxs-lookup"><span data-stu-id="e85a1-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="e85a1-178">Чрезмерное использование памяти может значительно сократить количество одновременных подключений.</span><span class="sxs-lookup"><span data-stu-id="e85a1-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
