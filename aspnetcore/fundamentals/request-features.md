---
title: Функции запросов в ASP.NET Core
author: ardalis
description: Сведения о реализации веб-сервера, связанные с HTTP-запросами и откликами, определяемые в интерфейсах для ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053713"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="b1ad0-103">Функции запросов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1ad0-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="b1ad0-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="b1ad0-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b1ad0-105">API `HttpContext`, используемый приложениями и ПО промежуточного слоя для обработки запросов, развернут поверх уровня абстракции *интерфейсов функций*.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces*.</span></span> <span data-ttu-id="b1ad0-106">Каждый интерфейс функций предоставляет подробно детализированный набор функциональных возможностей через `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="b1ad0-107">Эти интерфейсы можно добавлять, изменять, заключать в оболочки, заменять или даже удалять по требованию сервера или ПО промежуточного слоя по мере обработки запроса, не создавая полностью новую реализацию `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="b1ad0-108">Их также можно использовать для имитации функций при тестировании.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="b1ad0-109">Коллекции функций</span><span class="sxs-lookup"><span data-stu-id="b1ad0-109">Feature collections</span></span>

<span data-ttu-id="b1ad0-110">Свойство <xref:Microsoft.AspNetCore.Http.HttpContext.Features> из `HttpContext` предоставляет доступ к коллекции интерфейсов функций для текущего запроса.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="b1ad0-111">Так как коллекция функций является изменяемой даже внутри контекста запроса, ПО промежуточного слоя можно использовать для изменения этой коллекции и добавления поддержки дополнительных функций.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="b1ad0-112">Некоторые дополнительные функции доступны только через связанный интерфейс и коллекцию функций.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="b1ad0-113">Интерфейсы функций</span><span class="sxs-lookup"><span data-stu-id="b1ad0-113">Feature interfaces</span></span>

<span data-ttu-id="b1ad0-114">ASP.NET Core определяет в <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> несколько интерфейсов для стандартных функций HTTP, которые являются общими для многих серверов и ПО промежуточного слоя, чтобы отслеживать поддерживаемые ими функции.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="b1ad0-115">Серверы и ПО промежуточного слоя могут также предоставлять собственные интерфейсы с дополнительными функциональными возможностями.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="b1ad0-116">Большинство интерфейсов функций поддерживают необязательные облегченные функции, а связанные с ними API `HttpContext` возвращают варианты по умолчанию, если реализация функции отсутствует.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="b1ad0-117">Несколько интерфейсов далее определены как обязательные, так как они реализуют основные функции запросов и ответов, которые необходимы для обработки запроса.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="b1ad0-118">В <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> доступны следующие интерфейсы функций:</span><span class="sxs-lookup"><span data-stu-id="b1ad0-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="b1ad0-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Определяет структуру HTTP-запроса, включая протокол, путь, строку запроса, заголовки и основной текст.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="b1ad0-120">Эта функция необходима для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="b1ad0-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: определяет структуру ответа HTTP, включая код состояния, заголовки и основной текст ответа.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="b1ad0-122">Эта функция необходима для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1ad0-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: определяет разные способы для записи текста ответа, например в форматах `Stream`, `PipeWriter` или файла.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="b1ad0-124">Эта функция необходима для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="b1ad0-125">Она заменяет собой `IHttpResponseFeature.Body` и `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="b1ad0-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: содержит объект <xref:System.Security.Claims.ClaimsPrincipal>, который в данный момент связан с запросом.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="b1ad0-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: используется для анализа и кэширования входящих HTTP-запросов и многоэтапных форм.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b1ad0-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: позволяет указать, разрешены ли синхронные операции ввода-вывода для текста запроса или ответа.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1ad0-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: определяет методы для отключения буферизации запросов и (или) ответов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="b1ad0-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: определяет свойства для идентификатора подключения, локальных и удаленных адресов, а также портов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b1ad0-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: определяет максимально допустимый размер текста запроса для текущего запроса.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b1ad0-132">`IHttpRequestBodyDetectionFeature`: указывает, может ли запрос иметь текст.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="b1ad0-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: добавляет свойство, которое можно реализовать для уникальной идентификации запросов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="b1ad0-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: включает поддержку прерывания подключений или обнаружения преждевременного завершения запроса, например при отключении клиента.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1ad0-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: предоставляет доступ к заголовкам Trailer запросов при их наличии.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="b1ad0-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: используется для отправки сообщений о сбросе для протоколов, поддерживающих такие сообщения, например для HTTP/2 или HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b1ad0-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: позволяет приложению предоставлять заголовки Trailer ответов, если они поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1ad0-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: определяет метод для асинхронной отправки файлов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="b1ad0-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: определяет поддержку [обновлений HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), что позволяет клиенту указать дополнительные протоколы, которые требуется использовать, когда серверу нужно сменить протоколы.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="b1ad0-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: определяет API для поддержки веб-сокетов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1ad0-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: определяет, следует ли использовать сжатие ответов при подключениях по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="b1ad0-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: хранит коллекцию <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> для состояний приложения каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="b1ad0-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: выполняет анализ и кэширование строки запроса.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1ad0-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: представляет текст запроса в формате <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="b1ad0-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: выполняет анализ и кэширование значений из заголовков `Cookie` запросов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="b1ad0-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: управляет применением файлов cookie ответа к заголовку `Set-Cookie`.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b1ad0-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: эта функция предоставляет доступ к переменным сервера запросов, которые предоставляются службами IIS.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="b1ad0-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: предоставляет доступ к <xref:System.IServiceProvider> для служб запросов с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="b1ad0-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: определяет абстрактные классы `ISessionFactory` и <xref:Microsoft.AspNetCore.Http.ISession> для поддержки пользовательских сеансов.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="b1ad0-150">`ISessionFeature` реализуется в <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (см. <xref:fundamentals/app-state>).</span><span class="sxs-lookup"><span data-stu-id="b1ad0-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="b1ad0-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: определяет API для получения сертификатов клиента.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="b1ad0-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: определяет методы для работы с параметрами привязки маркера TLS.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="b1ad0-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: используется для запроса, предоставления и отзыва согласий пользователя в отношении хранения сведений о пользователе, связанных с работой и функциональностью сайта.</span><span class="sxs-lookup"><span data-stu-id="b1ad0-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b1ad0-154">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b1ad0-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
