---
title: Функции запросов в ASP.NET Core
author: ardalis
description: Сведения о реализации веб-сервера, связанные с HTTP-запросами и откликами, определяемые в интерфейсах для ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
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
uid: fundamentals/request-features
ms.openlocfilehash: 879b775ba2998ee803708ebf231b5fcd363b811c
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326432"
---
# <a name="request-features-in-aspnet-core"></a>Функции запросов в ASP.NET Core

Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)

API `HttpContext`, используемый приложениями и ПО промежуточного слоя для обработки запросов, развернут поверх уровня абстракции *интерфейсов функций* . Каждый интерфейс функций предоставляет подробно детализированный набор функциональных возможностей через `HttpContext`. Эти интерфейсы можно добавлять, изменять, заключать в оболочки, заменять или даже удалять по требованию сервера или ПО промежуточного слоя по мере обработки запроса, не создавая полностью новую реализацию `HttpContext`. Их также можно использовать для имитации функций при тестировании.

## <a name="feature-collections"></a>Коллекции функций

Свойство <xref:Microsoft.AspNetCore.Http.HttpContext.Features> из `HttpContext` предоставляет доступ к коллекции интерфейсов функций для текущего запроса. Так как коллекция функций является изменяемой даже внутри контекста запроса, ПО промежуточного слоя можно использовать для изменения этой коллекции и добавления поддержки дополнительных функций. Некоторые дополнительные функции доступны только через связанный интерфейс и коллекцию функций.

## <a name="feature-interfaces"></a>Интерфейсы функций

ASP.NET Core определяет в <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> несколько интерфейсов для стандартных функций HTTP, которые являются общими для многих серверов и ПО промежуточного слоя, чтобы отслеживать поддерживаемые ими функции. Серверы и ПО промежуточного слоя могут также предоставлять собственные интерфейсы с дополнительными функциональными возможностями.

Большинство интерфейсов функций поддерживают необязательные облегченные функции, а связанные с ними API `HttpContext` возвращают варианты по умолчанию, если реализация функции отсутствует. Несколько интерфейсов далее определены как обязательные, так как они реализуют основные функции запросов и ответов, которые необходимы для обработки запроса.

В <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> доступны следующие интерфейсы функций:

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Определяет структуру HTTP-запроса, включая протокол, путь, строку запроса, заголовки и основной текст. Эта функция необходима для обработки запросов.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: определяет структуру ответа HTTP, включая код состояния, заголовки и основной текст ответа. Эта функция необходима для обработки запросов.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: определяет разные способы для записи текста ответа, например в форматах `Stream`, `PipeWriter` или файла. Эта функция необходима для обработки запросов. Она заменяет собой `IHttpResponseFeature.Body` и `IHttpSendFileFeature`.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: содержит объект <xref:System.Security.Claims.ClaimsPrincipal>, который в данный момент связан с запросом.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: используется для анализа и кэширования входящих HTTP-запросов и многоэтапных форм.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: позволяет указать, разрешены ли синхронные операции ввода-вывода для текста запроса или ответа.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: определяет методы для отключения буферизации запросов и (или) ответов.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: определяет свойства для идентификатора подключения, локальных и удаленных адресов, а также портов.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: определяет максимально допустимый размер текста запроса для текущего запроса.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: указывает, может ли запрос иметь текст.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: добавляет свойство, которое можно реализовать для уникальной идентификации запросов.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: включает поддержку прерывания подключений или обнаружения преждевременного завершения запроса, например при отключении клиента.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: предоставляет доступ к заголовкам Trailer запросов при их наличии.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: используется для отправки сообщений о сбросе для протоколов, поддерживающих такие сообщения, например для HTTP/2 или HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: позволяет приложению предоставлять заголовки Trailer ответов, если они поддерживаются.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: определяет метод для асинхронной отправки файлов.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: определяет поддержку [обновлений HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), что позволяет клиенту указать дополнительные протоколы, которые требуется использовать, когда серверу нужно сменить протоколы.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: определяет API для поддержки веб-сокетов.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: определяет, следует ли использовать сжатие ответов при подключениях по протоколу HTTPS.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: хранит коллекцию <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> для состояний приложения каждого запроса.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: выполняет анализ и кэширование строки запроса.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: представляет текст запроса в формате <xref:System.IO.Pipelines.PipeReader>.
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: выполняет анализ и кэширование значений из заголовков `Cookie` запросов.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: управляет применением файлов cookie ответа к заголовку `Set-Cookie`.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: эта функция предоставляет доступ к переменным сервера запросов, которые предоставляются службами IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: предоставляет доступ к <xref:System.IServiceProvider> для служб запросов с заданной областью.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: определяет абстрактные классы `ISessionFactory` и <xref:Microsoft.AspNetCore.Http.ISession> для поддержки пользовательских сеансов. `ISessionFeature` реализуется в <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (см. <xref:fundamentals/app-state>).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: определяет API для получения сертификатов клиента.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: определяет методы для работы с параметрами привязки маркера TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: используется для запроса, предоставления и отзыва согласий пользователя в отношении хранения сведений о пользователе, связанных с работой и функциональностью сайта.
   
::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
