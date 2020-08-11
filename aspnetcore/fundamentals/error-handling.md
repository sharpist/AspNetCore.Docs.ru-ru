---
title: Обработка ошибок в ASP.NET Core
author: rick-anderson
description: Узнайте, как обрабатывать ошибки в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 7bc21901fe1e9ddf604abf3b5bfecdb8a319f12c
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444097"
---
# <a name="handle-errors-in-aspnet-core"></a>Обработка ошибок в ASP.NET Core

Авторы: [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)

В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core. Для веб-API см. раздел <xref:web-api/handle-errors>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). См. раздел [Загрузка примера](xref:index#how-to-download-a-sample). В этой статье содержатся инструкции о том, как задать директивы препроцессора (`#if`, `#endif`, `#define`) в примере для выполнения различных сценариев.

## <a name="developer-exception-page"></a>Страница со сведениями об исключении для разработчика

*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов. Эта страница доступна в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) из [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Добавьте код в метод `Startup.Configure`, чтобы включить страницу во время выполнения приложения в [среде](xref:fundamentals/environments) разработки:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Поместите вызов к <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> перед ПО промежуточного слоя, которое должно перехватывать исключения.

> [!WARNING]
> Включать страницу исключений для разработчика следует **только тогда, когда приложение выполняется в среде разработки**. Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде. Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.

Страница содержит следующие сведения об исключении и запросе:

* Трассировка стека
* параметры строки запроса (при наличии);
* файлы cookie (при наличии);
* Заголовки

Чтобы просмотреть страницу исключений для разработчика в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директиву препроцессора `DevEnvironment`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).

## <a name="exception-handler-page"></a>Страница обработчика исключений

Чтобы настроить пользовательскую страницу обработки ошибок для рабочей среды, используйте ПО промежуточного слоя для обработки исключений. ПО промежуточного слоя выполняет такие функции:

* Перехватывает и записывает в журнал исключения.
* Повторно выполняет запрос в альтернативном конвейере для указанной страницы или контроллера. Запрос не выполняется повторно, если запущен отклик.

В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Шаблон приложения Razor Pages предоставляет страницу ошибки (*CSHTML*) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*. Для приложения MVC шаблон проекта содержит метод действия при возникновении ошибки и представление ошибок. Ниже показан метод действия.

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`. Из-за использования явных команд некоторые запросы могут не передаваться в метод. Разрешите анонимный доступ к методу, чтобы не прошедшие проверку подлинности пользователи могли открывать представление ошибок.

### <a name="access-the-exception"></a>Доступ к исключению

Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в контроллере или на странице обработчика ошибок:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Не** передавайте клиентам конфиденциальную информацию об ошибках. Сохранение ошибок создает риски для безопасности.

Чтобы просмотреть страницу обработки ошибок в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerPage`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).

## <a name="exception-handler-lambda"></a>Лямбда-функция для обработчика исключений

Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.

Ниже приведен пример использования лямбда-функции для обработки исключений:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

В приведенном выше коде добавляется `await context.Response.WriteAsync(new string(' ', 512));`, поэтому браузер Internet Explorer отображает сообщение об ошибке, а не сообщение об ошибке IE. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>. Сохранение ошибок создает риски для безопасности.

Чтобы просмотреть результат обработки ошибок лямбда-функцией в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerLambda`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).

## <a name="usestatuscodepages"></a>UseStatusCodePages

По умолчанию приложение ASP.NET Core не предоставляет страницы для кодов состояния HTTP, таких как код *404 Not Found* (не найдено). Приложение возвращает код состояния без текста ответа. Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.

Это ПО промежуточного слоя доступно в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) из [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> в методе `Startup.Configure`.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов (например ПО промежуточного слоя для статических файлов и ПО промежуточного слоя MVC).

Вот пример текста, отображаемого обработчиками по умолчанию:

```
Status Code: 404; Not Found
```

Чтобы просмотреть различные форматы страницы кода состояния в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте одну из директив препроцессора, которые начинаются с `StatusCodePages`, а на домашней странице выберите **Trigger a 404** (Вызвать 404).

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages со строкой формата

Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает тип содержимого и строку формата.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages с лямбда-выражением

Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает лямбда-выражение.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Отправляет клиенту код состояния *302 — Found*.
* Перенаправляет клиента к расположению, предоставленному в шаблоне URL-адреса.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в примере. Если шаблон URL-адреса начинается с тильды (~), она заменяется `PathBase` приложения. Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки. Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.

Этот метод обычно используется, если приложение:

* Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку. Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.
* Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Возвращает исходный код состояния клиенту.
* Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки. Обязательно поместите `UseStatusCodePagesWithReExecute` перед `UseRouting`, чтобы запрос можно было перенаправить на страницу состояния. Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.

Этот метод обычно используется, если приложение:

* Обрабатывает запрос без перенаправления к другой конечной точке. Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.
* Сохраняет и возвращает исходный код состояния с ответом.

Шаблоны URL-адреса и строки запроса могут содержать заполнитель (`{0}`) для кода состояния. Шаблон URL-адреса должен начинаться с косой черты (`/`). При использовании заполнителя в пути убедитесь, что конечная точка (страница или контроллер) могут обрабатывать сегмент пути. Например, страница Razor для ошибок должна принимать необязательное значение сегмента с директивой `@page`.

```cshtml
@page "{code?}"
```

Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Отключение страниц с кодами состояния

Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Код обработки исключений

Код на страницах обработки исключений может создавать исключения. Часто желательно, чтобы страницы ошибок в рабочей среде содержали только статическое содержимое.

### <a name="response-headers"></a>Заголовки ответов

После отправки заголовков для ответа происходит следующее:

* Приложение не может изменить код состояния ответа.
* Нельзя запустить страницу или обработчик исключений. Необходимо завершить ответ или прервать подключение.

## <a name="server-exception-handling"></a>Обработка исключений на сервере

Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) может выполнять ряд операций по обработке исключений. Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ *500 Internal Server Error* (внутренняя ошибка сервера) без текста ответа. Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение. Запросы, не обработанные приложением, обрабатываются сервером. Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере. Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.

## <a name="startup-exception-handling"></a>Обработка исключений при запуске

Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения. Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).

На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла. При сбое привязки происходит следующее:

* На уровне размещения в журнале фиксируется критическое исключение.
* Выполняется аварийное завершение процесса dotnet.
* Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.

При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить. Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Страница ошибок базы данных

ПО промежуточного слоя для отображения страницы ошибок базы данных перехватывает исключения, относящиеся к базе данных, которые могут быть устранены при помощи миграций Entity Framework. При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы. Эту страницу следует включать только в среде разработки. Включите страницу, добавив код в `Startup.Configure`.

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

Для <xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> необходим пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Фильтры исключений

В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий. В приложениях Razor Pages они могут быть настроены глобально или для модели страницы. Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра. Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Но эти фильтры не так гибки, как ПО промежуточного слоя для обработки исключений. Мы рекомендуем использовать ПО промежуточного слоя. Используйте фильтры, только если ошибки нужно обрабатывать по-разному в зависимости от выбранного действия MVC.

## <a name="model-state-errors"></a>Ошибки состояния модели

Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
