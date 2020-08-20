---
title: Миграция обработчиков и модулей HTTP в ASP.NET Core по промежуточного слоя
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 8be09171991964540cd41a1324fb87503591151f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632178"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Миграция обработчиков и модулей HTTP в ASP.NET Core по промежуточного слоя

В этой статье показано, как перенести существующие [модули HTTP ASP.NET и обработчики с сервера System.](/iis/configuration/system.webserver/) в ASP.NET Core по [промежуточного слоя](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Повторно посещаемые модули и обработчики

Прежде чем перейти к ASP.NET Core по промежуточного слоя, давайте сначала посмотрим, как работают HTTP-модули и обработчики:

![Обработчик модулей](http-modules/_static/moduleshandlers.png)

**Обработчики:**

* Классы, реализующие [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Используется для обработки запросов с заданным именем или расширением файла, например *. Report*

* [Настроено](/iis/configuration/system.webserver/handlers/) в *Web.config*

**Модули:**

* Классы, реализующие интерфейс [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Вызывается для каждого запроса

* Возможность краткого замыкания (завершение дальнейшей обработки запроса)

* Возможность добавления в HTTP-ответ или создания собственных

* [Настроено](/iis/configuration/system.webserver/modules/) в *Web.config*

**Порядок, в котором модули обрабатывают входящие запросы, определяется следующим образом.**

1. [Жизненный цикл приложения](https://msdn.microsoft.com/library/ms227673.aspx)— это события серии, запускаемые ASP.NET: [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)и т. д. Каждый модуль может создать обработчик для одного или нескольких событий.

2. Для того же события порядок, в котором они настроены в *Web.config*.

В дополнение к модулям можно добавлять обработчики для событий жизненного цикла в файл *Global.asax.CS* . Эти обработчики запускаются после обработчиков в настроенных модулях.

## <a name="from-handlers-and-modules-to-middleware"></a>От обработчиков и модулей до по промежуточного слоя

**По промежуточного слоя проще, чем модули HTTP и обработчики:**

* Модули, обработчики, *Global.asax.CS*, *Web.config* (за исключением конфигурации IIS) и жизненного цикла приложения исчезают

* Роли обоих модулей и обработчиков были взяты по промежуточного слоя

* По промежуточного слоя настраивается с использованием кода, а не в *Web.config*

::: moniker range=">= aspnetcore-3.0"

* [Ветвление конвейера](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) позволяет отправлять запросы к определенному по промежуточного слоя, основываясь не только на URL-адресе, но также и в заголовках запросов, строках запросов и т. д.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* [Ветвление конвейера](xref:fundamentals/middleware/index#use-run-and-map) позволяет отправлять запросы к определенному по промежуточного слоя, основываясь не только на URL-адресе, но также и в заголовках запросов, строках запросов и т. д.

::: moniker-end

**По промежуточного слоя очень похоже на модули:**

* Вызывается в принципе для каждого запроса

* Возможность сокращения запроса путем [передачи запроса следующему по промежуточного слоя](#http-modules-shortcircuiting-middleware)

* Возможность создания собственного HTTP-ответа

**По промежуточного слоя и модули обрабатываются в другом порядке:**

* Порядок по промежуточного слоя основан на порядке, в котором они вставляются в конвейер запросов, а порядок модулей в основном основан на событиях [жизненного цикла приложения](https://msdn.microsoft.com/library/ms227673.aspx) .

* Порядок по промежуточного слоя для ответов является обратным по отношению к запросам, а порядок модулей одинаков для запросов и ответов

* См. раздел [создание конвейера по промежуточного слоя с помощью IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) .

![ПО промежуточного слоя](http-modules/_static/middleware.png)

Обратите внимание на то, как на приведенном выше рисунке промежуточное по проверки подлинности сокращено по запросу.

## <a name="migrating-module-code-to-middleware"></a>Перенос кода модуля на промежуточное по

Существующий HTTP-модуль будет выглядеть примерно так:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Как показано на странице по [промежуточного слоя](xref:fundamentals/middleware/index) , ASP.NET Core по промежуточного слоя представляет собой класс, который предоставляет `Invoke` метод, принимающий `HttpContext` и возвращающий `Task` . Новое по промежуточного слоя будет выглядеть следующим образом:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Предыдущий шаблон по промежуточного слоя был взят из раздела о [создании по промежуточного слоя](xref:fundamentals/middleware/write).

Вспомогательный класс *мимиддлеварикстенсионс* упрощает настройку по промежуточного слоя в `Startup` классе. `UseMyMiddleware`Метод добавляет ваш класс по промежуточного слоя в конвейер запросов. Службы, необходимые по промежуточного слоя, вставляются в конструктор по промежуточного слоя.

<a name="http-modules-shortcircuiting-middleware"></a>

Ваш модуль может завершить запрос, например, если пользователь не является полномочным:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

По промежуточного слоя обрабатывает это, не вызывая `Invoke` для следующего по промежуточного слоя в конвейере. Помните, что это не полностью завершает запрос, поскольку предыдущие промежуточные по по-прежнему будут вызываться, когда ответ обратно проходит через конвейер.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

При переносе функциональных возможностей модуля в новое по промежуточного слоя может оказаться, что код не компилируется, поскольку `HttpContext` класс сильно изменился в ASP.NET Core. [Позже](#migrating-to-the-new-httpcontext)вы увидите, как перейти на новый ASP.NET Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Перенос вставки модуля в конвейер запросов

Модули HTTP обычно добавляются в конвейер запросов с помощью *Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Преобразуйте это, [добавив новое по промежуточного слоя](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в конвейер запросов в своем `Startup` классе:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Точное место в конвейере, где вы вставляете новое по промежуточного слоя, зависит от события, которое оно обрабатывает как модуль ( `BeginRequest` , и `EndRequest` т. д.) и его порядок в списке модулей в *Web.config*.

Как было сказано выше, в ASP.NET Core нет жизненного цикла приложения, и порядок обработки ответов по промежуточного слоя отличается от порядка, используемого модулями. Это может сделать решение по упорядочиванию более сложным.

Если заказ станет проблемой, модуль можно разделить на несколько компонентов по промежуточного слоя, которые могут быть упорядочены независимо друг от друга.

## <a name="migrating-handler-code-to-middleware"></a>Перенос кода обработчика в по промежуточного слоя

Обработчик HTTP выглядит примерно так:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

В проекте ASP.NET Core это будет по промежуточного слоя, как показано ниже.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Это по промежуточного слоя очень похоже на по промежуточного слоя, соответствующее модулям. Единственная реальная разница заключается в том, что здесь нет вызова `_next.Invoke(context)` . Это имеет смысл, так как обработчик находится в конце конвейера запросов, поэтому следующее промежуточное по не будет вызываться.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Миграция вставки обработчика в конвейер запросов

Настройка обработчика HTTP выполняется в *Web.config* и выглядит примерно так:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Это можно преобразовать, добавив новое по промежуточного слоя обработчика в конвейер запросов в `Startup` классе, как и по промежуточного слоя, преобразованного из модулей. Проблема этого подхода заключается в том, что она будет отсылать все запросы на новый по промежуточного слоя обработчика. Однако запросы с заданным расширением должны достигать вашего по промежуточного слоя. Это позволит получить те же функциональные возможности, что и обработчик HTTP.

Одним из решений является ветвление конвейера для запросов с заданным расширением с помощью `MapWhen` метода расширения. Это делается в том же `Configure` методе, в котором вы добавляете другое по промежуточного слоя:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` принимает следующие параметры:

1. Лямбда-выражение, которое принимает `HttpContext` и возвращает, `true` Если запрос должен проходить по ветке. Это означает, что запросы ветвления можно выполнять не только на основе расширения, но и в заголовках запросов, параметрах строки запроса и т. д.

2. Лямбда-выражение, которое принимает `IApplicationBuilder` и добавляет все по промежуточного слоя для ветви. Это означает, что можно добавить дополнительное промежуточное по в ветвь перед по промежуточного слоя обработчика.

По промежуточного слоя, добавленное в конвейер до вызова ветви для всех запросов; ветвь не будет оказывать влияния на них.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Загрузка параметров по промежуточного слоя с помощью шаблона параметров

Некоторые модули и обработчики имеют параметры конфигурации, которые хранятся в *Web.config*. Однако в ASP.NET Core вместо *Web.config*используется новая модель конфигурации.

Новая [система конфигурации](xref:fundamentals/configuration/index) предоставляет следующие возможности для решения этой задачи:

* Непосредственно внедрять параметры в по промежуточного слоя, как показано в [следующем разделе](#loading-middleware-options-through-direct-injection).

* Используйте [шаблон параметров](xref:fundamentals/configuration/options):

1. Создайте класс для хранения параметров по промежуточного слоя, например:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Сохранение значений параметров

   Система конфигурации позволяет хранить значения параметров в любом месте. Однако большинство сайтов использует *appsettings.jsв*, поэтому мы будем использовать такой подход:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *Мимиддлевареоптионссектион* здесь — это имя раздела. Оно не должно совпадать с именем класса параметров.

3. Связывание значений параметров с классом Options

    Шаблон параметров использует инфраструктуру внедрения зависимостей ASP.NET Core для связывания типа параметров (например, `MyMiddlewareOptions` ) с `MyMiddlewareOptions` объектом с фактическими параметрами.

    Обновите свой `Startup` класс:

   1. Если вы используете *appsettings.js*, добавьте его в построитель конфигураций в `Startup` конструкторе:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Настройте службу Options:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Свяжите свои параметры с классом Options:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Вставьте параметры в конструктор по промежуточного слоя. Это похоже на внедрение параметров в контроллер.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Метод расширения [усемиддлеваре](#http-modules-usemiddleware) , который добавляет по промежуточного слоя в, `IApplicationBuilder` отвечает за внедрение зависимостей.

   Это не ограничивается `IOptions` объектами. Любой другой объект, который требуется по промежуточного слоя, можно внедрить таким образом.

## <a name="loading-middleware-options-through-direct-injection"></a>Загрузка параметров по промежуточного слоя через прямую вставку

Шаблон параметров имеет преимущество, что он создает слабую связь между значениями параметров и их потребителями. После связывания класса Options с фактическими значениями параметров любой другой класс может получить доступ к параметрам через платформу внедрения зависимостей. Нет необходимости передавать значения параметров.

Это разбивается, если вы хотите использовать одно по промежуточного слоя дважды с различными параметрами. Например, по промежуточного слоя авторизации, используемого в разных ветвях, которые позволяют использовать разные роли. Нельзя связать два различных объекта Options с одним классом параметров.

Решение заключается в получении объектов Options с фактическими значениями параметров в `Startup` классе и передачей их непосредственно в каждый экземпляр по промежуточного слоя.

1. Добавьте второй ключ для *appsettings.jsна*

   Чтобы добавить второй набор параметров к *appsettings.jsв* файле, используйте новый ключ для уникальной идентификации.

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Извлеките значения параметров и передайте их в по промежуточного слоя. `Use...`Метод расширения (который добавляет свое по промежуточного слоя в конвейер) является логическим местом для передачи значений параметров: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Включение по промежуточного слоя для использования параметра options. Предоставьте перегрузку `Use...` метода расширения (который принимает параметр options и передает его в `UseMiddleware` ). Когда `UseMiddleware` вызывается с параметрами, он передает параметры в конструктор по промежуточного слоя при создании экземпляра объекта по промежуточного слоя.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Обратите внимание, что это создает оболочку для объекта Options в `OptionsWrapper` объекте. В этом `IOptions` случае реализуется, как и ожидается конструктором по промежуточного слоя.

## <a name="migrating-to-the-new-httpcontext"></a>Переход на новый элемент HttpContext

Ранее было показано, что `Invoke` метод в по промежуточного слоя принимает параметр типа `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` значительно изменился в ASP.NET Core. В этом разделе показано, как преобразовать наиболее часто используемые свойства [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) в новый `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**Объекты HttpContext. Items** преобразуются в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Уникальный идентификатор запроса (не является аналогом System. Web. HttpContext)**

Предоставляет уникальный идентификатор для каждого запроса. Очень полезно включить в журналы.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. Request

**HttpContext. Request. HttpMethod** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**Запрос HttpContext. Request. QueryString** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. Request. URL** и **HttpContext. Request. равурл** преобразуются в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. иссекуреконнектион** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. усерхостаддресс** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. Cookie ** преобразует в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**Запрос HttpContext. Request. RequestContext. RouteData** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**Запросы HttpContext. Request. Headers** преобразуются в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. урлреферрер** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

Преобразование **HttpContext. Request. ContentType** в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

Преобразование **HttpContext. Request. Form** в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Чтение значений формы происходит только в том случае, если подтип содержимого имеет вид *x-www-Form-UrlEncoded* или *form-data*.

**HttpContext. Request. InputStream** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Этот код следует использовать только в по промежуточного слоя типа обработчика в конце конвейера.
>
>Вы можете прочитать необработанный текст, как показано выше, для каждого запроса. По промежуточного слоя, пытающегося прочитать текст после первого чтения, будет считать пустое тело.
>
>Это не относится к чтению формы, как показано выше, так как это делается из буфера.

### <a name="httpcontextresponse"></a>HttpContext. Response

**HttpContext. Response. status** и **HttpContext. Response. StatusDescription** транслируются в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

Преобразование **HttpContext. Response. ContentEncoding** и **HttpContext. Response. ContentType** в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext. Response. ContentType** также преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** преобразуется в:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

[Здесь](../fundamentals/request-features.md#middleware-and-request-features)обсуждается обслуживание файла.

**HttpContext. Response. Headers**

Отправка заголовков ответа осложняется тем, что если вы задали их после того, как что-либо записало в текст ответа, они не будут отправлены.

Решением является установка метода обратного вызова, который будет вызываться прямо перед началом записи в ответ. Это лучше сделать в начале `Invoke` метода по промежуточного слоя. Это метод обратного вызова, который задает заголовки ответа.

Следующий код задает метод обратного вызова с именем `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders`Метод обратного вызова будет выглядеть следующим образом:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie #d0**

Cookieперемещаются в браузер в заголовке *Set Cookie -* Response. В результате для отправки сообщений cookie s требуется тот же ответный вызов, который используется для отправки заголовков ответа:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies`Метод обратного вызова будет выглядеть следующим образом:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общие сведения о HTTP-обработчиках и модулях HTTP](/iis/configuration/system.webserver/)
* [Конфигурация](xref:fundamentals/configuration/index)
* [Запуск приложения](xref:fundamentals/startup)
* [ПО промежуточного слоя](xref:fundamentals/middleware/index)
