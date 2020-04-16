---
title: Включить запросы cross-Origin (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как CORS как стандарт для разрешения или отклонения запросов на кросс-происхождение в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440913"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Включить запросы cross-Origin (CORS) в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Рик Андерсон](https://twitter.com/RickAndMSFT) и [Кирк Ларкин](https://twitter.com/serpent5)

В этой статье показано, как включить CORS в ASP.NET приложение Core.

Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу. Это ограничение называется *политикой одного и того же происхождения.* Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта. Иногда может потребоваться разрешить другим сайтам делать запросы на кросс-происхождение в ваше приложение. Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)

[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):

* Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.
* Это **не** функция безопасности, CORS ослабляет безопасность. API не является более безопасным, позволяя CORS. Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).
* Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.
* Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [(как скачать)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>То же происхождение

Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).

Эти два URL-адреса имеют одно и то же происхождение:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:

* `https://example.net`&ndash; Различные домены
* `https://www.example.com/foo.html`&ndash; Различные поддомены
* `http://example.com/foo.html`&ndash; Различная схема
* `https://example.com:9000/foo.html`&ndash; Различные порты

## <a name="enable-cors"></a>Включение CORS

Существует три способа включить CORS:

* В промежуточном программном обеспечении с использованием [политики именованного](#np) значения или [политики по умолчанию.](#dp)
* Использование [конечных точек.](#ecors)
* С атрибутом [«EnableCors».](#attr)

Использование атрибута [«EnableCors»](#attr) с именем политики обеспечивает лучший контроль в ограничении конечных точек, поддерживающих CORS.

Каждый подход подробно описан в следующих разделах.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS с названной политикой и программой

CORS Middleware обрабатывает запросы по перекрестному происхождению. Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными истоками:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Предыдущий код:

* Устанавливает имя политики на `_myAllowSpecificOrigins`. Название политики является произвольным.
* Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и определяет `_myAllowSpecificOrigins` политику CORS. `UseCors`добавляет промежуточное программное обеспечение CORS.
* Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет. [Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.
* Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера. [См. конечную точку,](#ecors) чтобы применить политику CORS к определенным конечным точкам.

При приведении в конечный пункт разгром промежуточное программное `UseRouting` `UseEndpoints`обеспечение CORS ***должно*** быть настроено для выполнения между вызовами и .

Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.

Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Для получения дополнительной информации в этом документе [ознакомьтесь с вариантами политики CORS.](#cpo)

Методы <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> могут быть прикованы, как показано в следующем коде:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Примечание: Указанный URL **не** должен содержать`/`задняя черта (). Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS с политикой по умолчанию и программами среднего

Следующий выделенный код позволяет политику CORS по умолчанию:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Предыдущий код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Включение CORS с маршрутизацией конечных точек

Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf) Для получения дополнительной информации, [см. этот вопрос GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тест CORS с конечным пунктом разгрома и "HttpOptions"](#tcer).

При конечной конечной конечной реунетировке CORS может <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> быть включен на основе конечной точки с использованием набора методов расширения:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

В приведенном выше коде:

* `app.UseCors`позволяет CORS промежуточного посуды. Поскольку политика по умолчанию не `app.UseCors()` настроена, само по себе не позволяет CORS.
* Конечные `/echo` точки контроллера и контроллера позволяют запросы на перекрестное происхождение с помощью указанной политики.
* Конечные `/echo2` точки Страниц ы и страниц ы бритвы ***не*** разрешают запросы на перекрестное происхождение, поскольку политика по умолчанию не указана.

Атрибут [«DisableCors»](#dc) ***не*** отменяет CORS, который был включен путем `RequireCors`входиной раритетной коррески с помощью.

Ознакомиться с инструкциями по коду тестирования, аналогичным предыдущим, можно ознакомиться [с тестовым CORS с конечным точечным и «HttpOptions».](#tcer)

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Включить CORS с атрибутами

Включение CORS с атрибутом [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение политики с именем только к тем конечным точкам, которые требуют CORS, обеспечивает лучший контроль.

Атрибут [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру. Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек:

* `[EnableCors]`определяет политику по умолчанию.
* `[EnableCors("{Policy String}")]`определяет именованные политики.

Атрибут `[EnableCors]` может быть применен к:

* Страница бритвы`PageModel`
* Контроллер
* Метод действия контроллера

Различные политики могут быть применены к контроллерам, `[EnableCors]` моделям страниц или методам действий с атрибутом. Когда `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, и CORS включен в промежуточном программном обеспечении, ***обе*** политики применяются. ***Мы рекомендуем не сочетать политики. Используйте*** ***атрибут или промежуточное программное обеспечение, а не оба в том же приложении.*** `[EnableCors]`

Следующий код применяет различную политику к каждому методу:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Следующий код создает две политики CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Для лучшего контроля за ограничением запросов CORS:

* Используйте `[EnableCors("MyPolicy")]` с именем политики.
* Не определяйте политику по умолчанию.
* Не используйте [конечную точку.](#ecors)

Код в следующем разделе соответствует предыдущему списку.

Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.

<a name="dc"></a>

### <a name="disable-cors"></a>Отключить CORS

Атрибут [«DisableCors»](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***не*** отменяет CORS, который был включен путем [разгрома конечных точек.](#ecors)

Следующий код определяет политику `"MyPolicy"`CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Следующий код отменяет CORS `GetValues2` для действия:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Предыдущий код:

* Не позволяет CORS с [конечной точкий разгрома.](#ecors)
* Не определяет [политику CORS по умолчанию.](#dp)
* Использует [«EnableCors» («MyPolicy»)](#attr) для включения политики `"MyPolicy"` CORS для контроллера.
* Отражает CORS `GetValues2` для метода.

Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [в Test CORS.](#testc)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Варианты политики CORS

В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:

* [Установить допустимое происхождение](#set-the-allowed-origins)
* [Установите допустимые методы HTTP](#set-the-allowed-http-methods)
* [Установите заголовок разрешенных запросов](#set-the-allowed-request-headers)
* [Установите открытые заголовки ответов](#set-the-exposed-response-headers)
* [Учетные данные в запросах по перекрестному происхождению](#credentials-in-cross-origin-requests)
* [Установить предполетный срок годности](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в . Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.

## <a name="set-the-allowed-origins"></a>Установить допустимое происхождение

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`). `AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.

> [!NOTE]
> Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт. Служба CORS возвращает недействительный ответ CORS, когда приложение настроено с помощью обоих методов.

`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок. Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Установите допустимые методы HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Позволяет любой метод HTTP:
* Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок. Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Установите заголовок разрешенных запросов

Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали [заголовки запросов автора,](https://xhr.spec.whatwg.org/#request)позвоните и укажите разрешенные заголовки:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`влияет на предполетные запросы и заголовок [заголовков Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

Соответствие политики CORS Middleware к `WithHeaders` определенным заголовкам, указанному в, возможно только в том случае, если заголовки, отправленные в `Access-Control-Request-Headers` точном соответствии заголовкам, указанным в. `WithHeaders`

Например, рассмотрим приложение, настроенное следующим образом:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS Middleware отклоняет предварительный запрос со `Content-Language` следующим заголовком запроса, потому `WithHeaders`что ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) не указан в:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Приложение возвращает *200 OK* ответ, но не отправить CORS заголовки обратно. Таким образом, браузер не пытается кросс-происхождения запроса.

### <a name="set-the-exposed-response-headers"></a>Установите открытые заголовки ответов

По умолчанию браузер не предоставляет приложению все заголовки ответов. Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).

Заголовки ответов, доступные по умолчанию:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Спецификация CORS называет эти заголовки *простыми заголовками ответов.* Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Учетные данные в запросах по перекрестному происхождению

Учетные данные требуют специальной обработки в запросе CORS. По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения. Учетные данные включают файлы cookie и схемы проверки подлинности HTTP. Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.

Использование `XMLHttpRequest` непосредственно:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Используя j'ери:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Сервер должен разрешить учетные данные. Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.

Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.

Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности. Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.

<a name="pref"></a>

## <a name="preflight-requests"></a>Предполетные запросы

Для некоторых запросов CORS браузер отправляет дополнительный запрос [OPTIONS,](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) прежде чем сделать фактический запрос. Этот запрос называется [предполетным запросом.](https://developer.mozilla.org/docs/Glossary/Preflight_request) Браузер может пропустить предполетный запрос, если ***все*** следующие условия верны:

* Метод запроса: GET, HEAD или POST.
* Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .
* Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект. Спецификация CORS называет эти заголовки [автор запрос заголовки](https://www.w3.org/TR/cors/#author-request-headers). Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.

Ниже приводится пример ответа, аналогичного предполетного запроса, сделанного из кнопки **«Put Test»** в разделе [Test CORS](#testc) этого документа.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Предполетный запрос использует метод [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Он может включать в себя следующие заголовки:

* [Метод доступа-Контроль-Запрос](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Метод HTTP, который будет использоваться для фактического запроса.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Список заголовков запросов, который приложение устанавливает на фактический запрос. Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .
* [Методы контроля доступа-разрешить](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Если предполетный запрос отклонен, приложение `200 OK` возвращает ответ, но не устанавливает заголовки CORS. Таким образом, браузер не пытается кросс-происхождения запроса. Пример отклоненного предполетного запроса можно просмотреть раздел [теста CORS](#testc) этого документа.

Используя инструменты F12, консоль приложение показывает ошибку, похожую на одну из следующих, в зависимости от браузера:

* Firefox: Кросс-Origin Запрос заблокирован: Та же политика происхождения `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`запрещает чтение удаленного ресурса на . (Причина: запрос CORS не увенчался успехом). [Дополнительные сведения](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chromium на основе: Доступhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5к получению на ' от происхождения 'https://cors3.azurewebsites.netбыл заблокирован политикой CORS: Ответ на предполетный запрос не проходит проверку контроля доступа: нет заголовка 'Access-Control-Allow-Origin' на запрашиваемом ресурсе. Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.

Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Браузеры не соответствуют тому, `Access-Control-Request-Headers`как они устанавливают. Если какой-либо:

* Заголовки настроены на что-либо, кроме`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>называется: Включите `Content-Type`по `Origin`крайней мере `Accept`, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Автоматический код предполетного запроса

При применении политики CORS:

* Глобально, позвонив `app.UseCors` в `Startup.Configure`.
* Использование `[EnableCors]` атрибута.

ASP.NET Core отвечает на предполетный запрос OPTIONS.

Включение CORS на основе конечных `RequireCors` точек, используя в настоящее ***время, не*** поддерживает автоматические предполетные запросы.

Элемент ы в разделе [Test CORS](#testc) этого документа демонстрирует такое поведение.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Атрибут «HttpOptions» для предполетных запросов

Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS. В некоторых сценариях это может быть не так. Например, использование [CORS с конечным пунктом разгрома.](#ecors)

Следующий код использует атрибут [«HttpOptions»](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) для создания конечных точек для запросов OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [тест CORS с конечным точечным routing и «HttpOptions».](#tcer)

### <a name="set-the-preflight-expiration-time"></a>Установить предполетный срок годности

Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос. Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Как работает CORS

В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.

* CORS **не** является функцией безопасности. CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.
  * Например, злоумышленник может использовать [кросс-сайт сценариев (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнить запрос на кросс-сайт на свой сайт с поддержкой CORS для кражи информации.
* API не является более безопасным, позволяя CORS.
  * Клиент (браузер) должен обеспечить соблюдение CORS. Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ. Например, любой из следующих инструментов будет отображать ответ сервера:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET httpclient](/dotnet/csharp/tutorials/console-webapiclient)
    * Веб-браузер, введя URL-адрес в адресной строке.
* Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.
  * Браузеры без CORS не могут делать запросы на кросс-происхождение. До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение. JSONP не использует XHR, он `<script>` использует тег для получения ответа. Скрипты могут быть загружены кросс-происхождения.

[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение. Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения. Пользовательский код JavaScript не требуется для включения CORS.

[Кнопка теста PUT](https://cors3.azurewebsites.net/test) на развернутом [образце](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Ниже приводится пример запроса на перекрестное происхождение `https://cors1.azurewebsites.net/api/values`от кнопки тестирования [значений.](https://cors3.azurewebsites.net/) Заголовок: `Origin`

* Предоставляет домен сайта, который делает запрос.
* Требуется и должен отличаться от хоста.

**Общие заголовки**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Заголовки ответов**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Заголовки запросов**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

В `OPTIONS` запросах сервер устанавливает заголовок **заголовков ответов** `Access-Control-Allow-Origin: {allowed origin}` в ответе. Например, развернутый [образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), Удалить кнопку ["EnableCors"](https://cors1.azurewebsites.net/test?number=2) кнопка `OPTIONS` запрос содержит следующие заголовки:

**Общие заголовки**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Заголовки ответов**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Заголовки запросов**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

В предыдущих **заголовках ответов**сервер устанавливает в ответ заголовок [Access-Control-Allow-Origin.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) Значение `https://cors1.azurewebsites.net` этого заголовка совпадает с заголовком `Origin` из запроса.

Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> называется, `Access-Control-Allow-Origin: *`значение подстановочного знака возвращается. `AllowAnyOrigin`позволяет любого происхождения.

Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается. В частности, браузер отдает запрос. Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.

<a name="options"></a>

### <a name="display-options-requests"></a>Отображение options запросов

По умолчанию браузеры Chrome и Edge не отображали запросы OPTIONS на сетевой вкладке инструментов F12. Для отображения запросов OPTIONS в этих браузерах:

* `chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`
* отключить флаг.
* Перезапустить.

Firefox показывает запросы OPTIONS по умолчанию.

## <a name="cors-in-iis"></a>КОРС в IIS

При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа. Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.

<a name="testc"></a>

## <a name="test-cors"></a>Тестирование CORS

[В загрузке образца](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS. См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample). Образец представляет собой проект API с добавлением страниц Razor:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

`ValuesController` Следующие точки для тестирования:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется [пакетом Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet и отображает информацию о маршруте.

Проверьте предыдущий пример кода, используя один из следующих подходов:

* Используйте развернутое [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)приложение образца на . Нет необходимости загружать образец.
* Выполнить образец с `dotnet run` помощью URL-адреса по `https://localhost:5001`умолчанию.
* Выполнить образец из Visual Studio с портом установлен на `https://localhost:44398`44398 для URL .

Использование браузера с инструментами F12:

* Выберите кнопку **Значения** и просмотрите заголовки во вкладке **Сети.**
* Выберите кнопку **теста PUT.** Смотрите [запросы Display OPTIONS](#options) для инструкций по отображению запроса OPTIONS. **Тест PUT** создает два запроса: предполетный запрос OPTIONS и put-запрос.
* Выберите **`GetValues2 [DisableCors]`** кнопку, чтобы вызвать неудавшийся запрос CORS. Как уже упоминалось в документе, ответ возвращает 200 успехов, но запрос CORS не сделан. Выберите вкладку **Консоль,** чтобы увидеть ошибку CORS. В зависимости от браузера отображается ошибка, аналогичная следующему:

     Доступ к `'https://cors1.azurewebsites.net/api/values/GetValues2'` получению `'https://cors3.azurewebsites.net'` из происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка «Доступ-контроль-разрешить-Origin». Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.
     
Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [локон,](https://curl.haxx.se/) [Скрипач](https://www.telerik.com/fiddler)или [Почтальон.](https://www.getpostman.com/) При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей. Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:

* Для обработки запроса CORS Middleware не нужно.
* Заголовки CORS не возвращаются в ответ.

Следующая команда `curl` использует для выдачи запроса OPTIONS с информацией:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Тест CORS с конечным точечным разгромом и «HttpOptions»

Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf) Рассмотрим следующий код, который использует [конечную точку разгрома для включения CORS:](#ecors)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

`TodoItems1Controller` Следующие конечные точки для тестирования:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=1) развернутого [образца.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

**Кнопки «Удалить» и** **«GET» (EnableCors)** удаляют `[EnableCors]` ся, так как конечные точки имеют и отвечают на предполетные запросы. Другие конечные точки не удается. Кнопка **GET** выходит из строя, так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Следующие `TodoItems2Controller` конечные точки, но включает в себя явный код для ответа на запросы OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=2) развернутого образца. В **контроллере** упасть список, выберите **Preflight,** а затем **установить контроллер.** Все вызовы CORS `TodoItems2Controller` в конечные точки увенчаются успехом.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общий доступ к ресурсам независимо от источника (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Начало работы с модулем IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)

В этой статье показано, как включить CORS в ASP.NET приложение Core.

Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу. Это ограничение называется *политикой одного и того же происхождения.* Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта. Иногда может потребоваться разрешить другим сайтам делать запросы на перекрестное происхождение в вашем приложении. Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)

[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):

* Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.
* Это **не** функция безопасности, CORS ослабляет безопасность. API не является более безопасным, позволяя CORS. Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).
* Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.
* Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) [(как скачать)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>То же происхождение

Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).

Эти два URL-адреса имеют одно и то же происхождение:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:

* `https://example.net`&ndash; Различные домены
* `https://www.example.com/foo.html`&ndash; Различные поддомены
* `http://example.com/foo.html`&ndash; Различная схема
* `https://example.com:9000/foo.html`&ndash; Различные порты

Internet Explorer не учитывает порт при сравнении происхождения.

## <a name="cors-with-named-policy-and-middleware"></a>CORS с названной политикой и программой

CORS Middleware обрабатывает запросы по перекрестному происхождению. Следующий код позволяет CORS для всего приложения с указанным происхождением:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Предыдущий код:

* Устанавливает название полиса\_на "myAllowSpecificOrigins". Название политики является произвольным.
* Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который позволяет CORS.
* Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет. [Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.

Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Для получения дополнительной информации [в](#cpo) этом документе см.

Метод <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> может цепиметоды, как показано в следующем коде:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Примечание: **URL-адрес не** должен содержать`/`задняя черта (). Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.

Следующий код применяет политики CORS ко всем конечным точкам приложений через CORS Middleware:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Примечание: `UseCors` необходимо вызвать перед `UseMvc`.

[См. Включить CORS в Razor Страницы, контроллеры и методы действий](#ecors) для применения политики CORS на странице / контроллер / действие уровне.

Ознакомиться с [инструкциями](#test) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.

## <a name="enable-cors-with-attributes"></a>Включить CORS с атрибутами

Атрибут [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру. Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек.

Используется `[EnableCors]` для указания политики по умолчанию и `[EnableCors("{Policy String}")]` для указания политики.

Атрибут `[EnableCors]` может быть применен к:

* Страница бритвы`PageModel`
* Контроллер
* Метод действия контроллера

Можно применить различные политики к контроллеру/странице-модели/действию с атрибутом. `[EnableCors]` Когда `[EnableCors]` атрибут применяется к методу модели/модели/действий контроллеров/страниц, а CORS включен в промежуточном программном обеспечении, ***применяются обе*** политики. Мы рекомендуем ***не*** комбинировать политики. Используйте `[EnableCors]` атрибут или промежуточное программное обеспечение, и**то, и другое.** При `[EnableCors]`использовании **не** определяется политика по умолчанию.

Следующий код применяет различную политику к каждому методу:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Следующий код создает политику по умолчанию `"AnotherPolicy"`CORS и политику под названием:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Отключить CORS

[ &lbrack;Атрибут DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отстраняет CORS от контроллера/страницы-модели/действия.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Варианты политики CORS

В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:

* [Установить допустимое происхождение](#set-the-allowed-origins)
* [Установите допустимые методы HTTP](#set-the-allowed-http-methods)
* [Установите заголовок разрешенных запросов](#set-the-allowed-request-headers)
* [Установите открытые заголовки ответов](#set-the-exposed-response-headers)
* [Учетные данные в запросах по перекрестному происхождению](#credentials-in-cross-origin-requests)
* [Установить предполетный срок годности](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в . Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.

## <a name="set-the-allowed-origins"></a>Установить допустимое происхождение

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`). `AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.

> [!NOTE]
> Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт. Для безопасного приложения укажите точный список истоков, если клиент должен разрешить себе доступ к серверным ресурсам.

`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок. Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Установите допустимые методы HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Позволяет любой метод HTTP:
* Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок. Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Установите заголовок разрешенных запросов

Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали *заголовки запросов автора,* позвоните и укажите разрешенные заголовки:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Эта настройка влияет на `Access-Control-Request-Headers` предполетные запросы и заголовок. Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)

CORS Middleware всегда позволяет отправлять `Access-Control-Request-Headers` четыре заголовка, которые должны быть отправлены независимо от значений, настроенных в CorsPolicy.Headers. Этот список заголовков включает в себя:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Например, рассмотрим приложение, настроенное следующим образом:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware успешно отвечает на предполетный запрос следующим `Content-Language` заголовком запроса, поскольку всегда находится в белом списке:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Установите открытые заголовки ответов

По умолчанию браузер не предоставляет приложению все заголовки ответов. Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).

Заголовки ответов, доступные по умолчанию:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Спецификация CORS называет эти заголовки *простыми заголовками ответов.* Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Учетные данные в запросах по перекрестному происхождению

Учетные данные требуют специальной обработки в запросе CORS. По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения. Учетные данные включают файлы cookie и схемы проверки подлинности HTTP. Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.

Использование `XMLHttpRequest` непосредственно:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Используя j'ери:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Сервер должен разрешить учетные данные. Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.

Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.

Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности. Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.

### <a name="preflight-requests"></a>Предполетные запросы

Для некоторых запросов CORS браузер отправляет дополнительный запрос, прежде чем сделать фактический запрос. Этот запрос называется *предполетным запросом.* Браузер может пропустить предполетный запрос, если верны следующие условия:

* Метод запроса: GET, HEAD или POST.
* Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .
* Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект. Спецификация CORS называет эти заголовки *автор запрос заголовки*. Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.

Ниже приводится пример предполетного запроса:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Предполетный запрос использует метод HTTP OPTIONS. Она включает в себя два специальных заголовка:

* `Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.
* `Access-Control-Request-Headers`: Список заголовков запросов, которые приложение устанавливает на фактический запрос. Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .

<!-- I think this needs to be removed, was put here accidently -->

Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS. Смотрите [атрибут «HttpOptions» для предполетных запросов.](#pro)

Предполетный запрос CORS `Access-Control-Request-Headers` может включать заголовок, который указывает на сервер заголовки, которые отправляются с фактическим запросом.

Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Браузеры не полностью последовательны в `Access-Control-Request-Headers`том, как они устанавливают. Если вы установите заголовки `"*"` на <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>что-либо, кроме `Accept` `Content-Type`(или `Origin`использовать), вы должны включить по крайней мере, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.

Ниже приводится пример ответа на предполетный запрос (при условии, что сервер позволяет запрос):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Ответ включает `Access-Control-Allow-Methods` в себя заголовок, который перечисляет `Access-Control-Allow-Headers` разрешенные методы и опционально заголовок, в котором перечислены разрешенные заголовки. Если предполетный запрос удается, браузер отправляет фактический запрос.

Если предполетный запрос отклонен, приложение возвращает *200 OK* ответ, но не отправляет заголовки CORS обратно. Таким образом, браузер не пытается кросс-происхождения запроса.

### <a name="set-the-preflight-expiration-time"></a>Установить предполетный срок годности

Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос. Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Как работает CORS

В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.

* CORS **не** является функцией безопасности. CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.
  * Например, злоумышленник может использовать prevent [cross-Site Scripting (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнять запрос на перекрестный сайт на свой сайт с поддержкой CORS для кражи информации.
* Ваш API не является более безопасным, позволяя CORS.
  * Клиент (браузер) должен обеспечить соблюдение CORS. Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ. Например, любой из следующих инструментов будет отображать ответ сервера:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET httpclient](/dotnet/csharp/tutorials/console-webapiclient)
    * Веб-браузер, введя URL-адрес в адресной строке.
* Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.
  * Браузеры (без CORS) не могут делать запросы на кросс-происхождение. До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение. JSONP не использует XHR, он `<script>` использует тег для получения ответа. Скрипты могут быть загружены кросс-происхождения.

[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение. Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения. Пользовательский код JavaScript не требуется для включения CORS.

Ниже приводится пример запроса о перекрестном происхождении. Заголовок `Origin` предоставляет домен сайта, который делает запрос. Заголовок `Origin` необходим и должен отличаться от хостена.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Если сервер разрешает запрос, он `Access-Control-Allow-Origin` устанавливает заголовок в ответе. Значение этого заголовка либо `Origin` соответствует заголовку из запроса, `"*"`либо является значением подстановочного знака, что означает, что любое происхождение разрешено:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается. В частности, браузер отдает запрос. Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.

<a name="test"></a>

## <a name="test-cors"></a>Тестирование CORS

Для тестирования CORS:

1. [Создайте проект API](xref:tutorials/first-web-api). Кроме того, вы можете [скачать образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Включите CORS, используя один из подходов в этом документе. Пример:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Создайте проект веб-приложений (Страницы razor или MVC). В образце используются страницы razor. Вы можете создать веб-приложение в том же решении, что и проект API.
1. Добавьте следующий выделенный код в файл *Index.cshtml:*

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. В предыдущем коде `url: 'https://<web app>.azurewebsites.net/api/values/1',` замените URL-адрес развернутого приложения.
1. Развертывание проекта API. Например, [развернуть в Azure](xref:host-and-deploy/azure-apps/index).
1. Выполнить Razor Pages или MVC приложение с рабочего стола и нажмите на кнопку **испытаний.** Используйте инструменты F12 для проверки сообщений об ошибках.
1. Удалите происхождение `WithOrigins` локального хозяина из приложения и разместите его. Кроме того, запустите клиентское приложение с другим портом. Например, запустить из Visual Studio.
1. Тест с помощью клиентского приложения. Сбои CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript. Используйте консольную вкладку в инструментах F12, чтобы увидеть ошибку. В зависимости от браузера, вы получаете ошибку (в f12 консоли инструментов) похож на следующее:

   * Использование Microsoft Edge:

     **SEC7120: «CORS» `https://localhost:44375` Происхождение не `https://localhost:44375` было нашло в заголовке ответа Access-Control-Allow-Origin для ресурса кросс-происхождения в`https://webapi.azurewebsites.net/api/values/1`**

   * Использование Chrome:

     **Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` от происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка "Доступ-контроль-Разрешить-Происхождение".**
     
Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [Fiddler](https://www.telerik.com/fiddler) или [Postman.](https://www.getpostman.com/) При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей. Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:

* Для обработки запроса CORS Middleware не нужно.
* Заголовки CORS не возвращаются в ответ.

## <a name="cors-in-iis"></a>КОРС в IIS

При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа. Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общий доступ к ресурсам независимо от источника (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Начало работы с модулем IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
