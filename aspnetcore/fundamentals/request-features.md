---
title: Функции запросов в ASP.NET Core
author: ardalis
description: Сведения о реализации веб-сервера, связанные с HTTP-запросами и откликами, определяемые в интерфейсах для ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: fundamentals/request-features
ms.openlocfilehash: 4b6b34724bc848578293e586da12ead63c96cfbd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016795"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="9d58f-103">Функции запросов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d58f-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="9d58f-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="9d58f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9d58f-105">Сведения о реализации веб-сервера, связанные с HTTP-запросами и ответами, определяются в интерфейсах.</span><span class="sxs-lookup"><span data-stu-id="9d58f-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="9d58f-106">Эти интерфейсы используются реализациями сервера и ПО промежуточного слоя для создания и изменения конвейера размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="9d58f-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="9d58f-107">Интерфейсы функций</span><span class="sxs-lookup"><span data-stu-id="9d58f-107">Feature interfaces</span></span>

<span data-ttu-id="9d58f-108">ASP.NET Core определяет несколько интерфейсов функций HTTP в `Microsoft.AspNetCore.Http.Features`, которые используются серверами для определения поддерживаемых ими функций.</span><span class="sxs-lookup"><span data-stu-id="9d58f-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="9d58f-109">Следующие интерфейсы функций обрабатывают запросы и возвращают отклики:</span><span class="sxs-lookup"><span data-stu-id="9d58f-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="9d58f-110">`IHttpRequestFeature` определяет структуру HTTP-запроса, включая протокол, путь, строку запроса, заголовки и основной текст.</span><span class="sxs-lookup"><span data-stu-id="9d58f-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="9d58f-111">`IHttpResponseFeature` определяет структуру HTTP-отклика, включая код состояния, заголовки и основной текст отклика.</span><span class="sxs-lookup"><span data-stu-id="9d58f-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="9d58f-112">`IHttpAuthenticationFeature` определяет поддержку для идентификации пользователей на основе `ClaimsPrincipal` и указания обработчика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9d58f-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="9d58f-113">`IHttpUpgradeFeature` определяет поддержку для [обновлений HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), позволяющих клиенту указать дополнительные протоколы, которые требуется использовать, когда серверу нужно сменить протоколы.</span><span class="sxs-lookup"><span data-stu-id="9d58f-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="9d58f-114">`IHttpBufferingFeature` определяет методы для отключения буферизации запросов и (или) откликов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="9d58f-115">`IHttpConnectionFeature` определяет свойства для локальных и удаленных адресов и портов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="9d58f-116">`IHttpRequestLifetimeFeature` определяет поддержку для прерывания подключений или обнаружения преждевременного завершения запроса, например при отключении клиента.</span><span class="sxs-lookup"><span data-stu-id="9d58f-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="9d58f-117">`IHttpSendFileFeature` определяет метод для асинхронной передачи файлов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="9d58f-118">`IHttpWebSocketFeature` определяет API для поддержки веб-сокетов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="9d58f-119">`IHttpRequestIdentifierFeature` добавляет свойство, которое можно реализовать для уникальной идентификации запросов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="9d58f-120">`ISessionFeature` определяет абстрактные классы `ISessionFactory` и `ISession` для поддержки пользовательских сеансов.</span><span class="sxs-lookup"><span data-stu-id="9d58f-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="9d58f-121">`ITlsConnectionFeature` определяет API для получения сертификатов клиента.</span><span class="sxs-lookup"><span data-stu-id="9d58f-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="9d58f-122">`ITlsTokenBindingFeature` определяет методы для работы с параметрами привязки токена TLS.</span><span class="sxs-lookup"><span data-stu-id="9d58f-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="9d58f-123">`ISessionFeature` не является функцией сервера, но реализуется `SessionMiddleware` (см. раздел [Управление состоянием приложения](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="9d58f-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="9d58f-124">Коллекции функций</span><span class="sxs-lookup"><span data-stu-id="9d58f-124">Feature collections</span></span>

<span data-ttu-id="9d58f-125">Свойство `Features` объекта `HttpContext` предоставляет интерфейс для получения и задания доступных функций HTTP для текущего запроса.</span><span class="sxs-lookup"><span data-stu-id="9d58f-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="9d58f-126">Так как коллекция функций является изменяемой даже внутри контекста запроса, ПО промежуточного слоя можно использовать для изменения этой коллекции и добавления поддержки дополнительных функций.</span><span class="sxs-lookup"><span data-stu-id="9d58f-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="9d58f-127">ПО промежуточного слоя и функции запросов</span><span class="sxs-lookup"><span data-stu-id="9d58f-127">Middleware and request features</span></span>

<span data-ttu-id="9d58f-128">Хотя серверы отвечают за создание коллекции функций, ПО промежуточного слоя может как добавлять элементы в эту коллекцию, так и использовать функции из нее.</span><span class="sxs-lookup"><span data-stu-id="9d58f-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="9d58f-129">Например, `StaticFileMiddleware` обращается к функции `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="9d58f-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="9d58f-130">Если функция существует, она используется для отправки запрашиваемого статического файла из его физического пути.</span><span class="sxs-lookup"><span data-stu-id="9d58f-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="9d58f-131">В противном случае для отправки файла применяется более медленный альтернативный метод.</span><span class="sxs-lookup"><span data-stu-id="9d58f-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="9d58f-132">Когда функция `IHttpSendFileFeature` доступна, она позволяет операционной системе открыть файл и выполнить прямое копирование в режиме ядра на сетевую карту.</span><span class="sxs-lookup"><span data-stu-id="9d58f-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="9d58f-133">Кроме того, ПО промежуточного слоя может добавлять элементы в коллекцию функций, заданную сервером.</span><span class="sxs-lookup"><span data-stu-id="9d58f-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="9d58f-134">ПО промежуточного слоя даже может заменять существующие функции, что позволяет ему расширять функциональность сервера.</span><span class="sxs-lookup"><span data-stu-id="9d58f-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="9d58f-135">Добавляемые в коллекцию функции сразу же становятся доступными другому ПО промежуточного слоя или самому базовому приложению на более позднем этапе конвейера.</span><span class="sxs-lookup"><span data-stu-id="9d58f-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="9d58f-136">Объединив пользовательские реализации сервера и улучшения ПО промежуточного слоя, можно создать именно тот набор функций, который необходим приложению.</span><span class="sxs-lookup"><span data-stu-id="9d58f-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="9d58f-137">Это позволяет добавлять отсутствующие функции без внесения изменений на сервере, а также предоставлять лишь минимальный набор функций, что ограничивает направления атак и повышает производительность.</span><span class="sxs-lookup"><span data-stu-id="9d58f-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="9d58f-138">Сводка</span><span class="sxs-lookup"><span data-stu-id="9d58f-138">Summary</span></span>

<span data-ttu-id="9d58f-139">Интерфейсы функций определяют конкретные функции HTTP, которые может поддерживать указанный запрос.</span><span class="sxs-lookup"><span data-stu-id="9d58f-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="9d58f-140">Серверы определяют коллекции функций и первоначальный набор функций, поддерживаемый этим сервером, но эти функции можно расширить с помощью ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="9d58f-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d58f-141">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9d58f-141">Additional resources</span></span>

* [<span data-ttu-id="9d58f-142">Серверы</span><span class="sxs-lookup"><span data-stu-id="9d58f-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="9d58f-143">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="9d58f-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="9d58f-144">Открытый веб-интерфейс для .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="9d58f-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
