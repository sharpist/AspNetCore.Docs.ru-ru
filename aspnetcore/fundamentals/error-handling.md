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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: bbd16e2e045a24f018eb59b67dfdf50d9bed6f32
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775041"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="fc7f4-103">Обработка ошибок в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc7f4-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="fc7f4-104">Авторы: [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="fc7f4-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fc7f4-105">В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="fc7f4-106">Для веб-API см. раздел <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="fc7f4-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="fc7f4-108">См. раздел [Загрузка примера](xref:index#how-to-download-a-sample). В этой статье содержатся инструкции о том, как задать директивы препроцессора (`#if`, `#endif`, `#define`) в примере для выполнения различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="fc7f4-109">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="fc7f4-109">Developer Exception Page</span></span>

<span data-ttu-id="fc7f4-110">*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="fc7f4-111">Эта страница доступна в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) из [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="fc7f4-112">Добавьте код в метод `Startup.Configure`, чтобы включить страницу во время выполнения приложения в [среде](xref:fundamentals/environments) разработки:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="fc7f4-113">Поместите вызов к <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> перед ПО промежуточного слоя, которое должно перехватывать исключения.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="fc7f4-114">Включать страницу исключений для разработчика следует **только тогда, когда приложение выполняется в среде разработки**.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="fc7f4-115">Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="fc7f4-116">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="fc7f4-117">Страница содержит следующие сведения об исключении и запросе:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="fc7f4-118">Трассировка стека</span><span class="sxs-lookup"><span data-stu-id="fc7f4-118">Stack trace</span></span>
* <span data-ttu-id="fc7f4-119">параметры строки запроса (при наличии);</span><span class="sxs-lookup"><span data-stu-id="fc7f4-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="fc7f4-120">файлы cookie (при наличии);</span><span class="sxs-lookup"><span data-stu-id="fc7f4-120">Cookies (if any)</span></span>
* <span data-ttu-id="fc7f4-121">Заголовки</span><span class="sxs-lookup"><span data-stu-id="fc7f4-121">Headers</span></span>

<span data-ttu-id="fc7f4-122">Чтобы просмотреть страницу исключений для разработчика в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директиву препроцессора `DevEnvironment`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="fc7f4-123">Страница обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="fc7f4-123">Exception handler page</span></span>

<span data-ttu-id="fc7f4-124">Чтобы настроить пользовательскую страницу обработки ошибок для рабочей среды, используйте ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="fc7f4-125">ПО промежуточного слоя выполняет такие функции:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-125">The middleware:</span></span>

* <span data-ttu-id="fc7f4-126">Перехватывает и записывает в журнал исключения.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="fc7f4-127">Повторно выполняет запрос в альтернативном конвейере для указанной страницы или контроллера.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="fc7f4-128">Запрос не выполняется повторно, если запущен отклик.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="fc7f4-129">В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="fc7f4-130">Шаблон приложения Razor Pages предоставляет страницу ошибки (*CSHTML*) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="fc7f4-131">Для приложения MVC шаблон проекта содержит метод действия при возникновении ошибки и представление ошибок.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="fc7f4-132">Ниже показан метод действия.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="fc7f4-133">Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="fc7f4-134">Из-за использования явных команд некоторые запросы могут не передаваться в метод.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="fc7f4-135">Разрешите анонимный доступ к методу, чтобы не прошедшие проверку подлинности пользователи могли открывать представление ошибок.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="fc7f4-136">Доступ к исключению</span><span class="sxs-lookup"><span data-stu-id="fc7f4-136">Access the exception</span></span>

<span data-ttu-id="fc7f4-137">Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в контроллере или на странице обработчика ошибок:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="fc7f4-138">**Не** передавайте клиентам конфиденциальную информацию об ошибках.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="fc7f4-139">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="fc7f4-140">Чтобы просмотреть страницу обработки ошибок в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerPage`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="fc7f4-141">Лямбда-функция для обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="fc7f4-141">Exception handler lambda</span></span>

<span data-ttu-id="fc7f4-142">Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="fc7f4-143">Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="fc7f4-144">Ниже приведен пример использования лямбда-функции для обработки исключений:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="fc7f4-145">В приведенном выше коде добавляется `await context.Response.WriteAsync(new string(' ', 512));`, поэтому браузер Internet Explorer отображает сообщение об ошибке, а не сообщение об ошибке IE.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="fc7f4-146">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="fc7f4-147">**Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="fc7f4-148">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="fc7f4-149">Чтобы просмотреть результат обработки ошибок лямбда-функцией в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerLambda`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="fc7f4-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="fc7f4-150">UseStatusCodePages</span></span>

<span data-ttu-id="fc7f4-151">По умолчанию приложение ASP.NET Core не предоставляет страницы для кодов состояния HTTP, таких как код *404 Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="fc7f4-152">Приложение возвращает код состояния без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="fc7f4-153">Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="fc7f4-154">Это ПО промежуточного слоя доступно в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) из [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="fc7f4-155">Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="fc7f4-156">Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов (например ПО промежуточного слоя для статических файлов и ПО промежуточного слоя MVC).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="fc7f4-157">Вот пример текста, отображаемого обработчиками по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="fc7f4-158">Чтобы просмотреть различные форматы страницы кода состояния в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте одну из директив препроцессора, которые начинаются с `StatusCodePages`, а на домашней странице выберите **Trigger a 404** (Вызвать 404).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="fc7f4-159">UseStatusCodePages со строкой формата</span><span class="sxs-lookup"><span data-stu-id="fc7f4-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="fc7f4-160">Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, которая принимает тип содержимого и строку формата.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="fc7f4-161">UseStatusCodePages с лямбда-выражением</span><span class="sxs-lookup"><span data-stu-id="fc7f4-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="fc7f4-162">Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, которая принимает лямбда-выражение.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="fc7f4-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="fc7f4-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="fc7f4-164">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="fc7f4-165">Отправляет клиенту код состояния *302 — Found*.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="fc7f4-166">Перенаправляет клиента к расположению, предоставленному в шаблоне URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="fc7f4-167">Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в примере.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="fc7f4-168">Если шаблон URL-адреса начинается с тильды (~), она заменяется `PathBase` приложения.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="fc7f4-169">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="fc7f4-170">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="fc7f4-171">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="fc7f4-172">Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="fc7f4-173">Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="fc7f4-174">Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="fc7f4-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="fc7f4-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="fc7f4-176">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="fc7f4-177">Возвращает исходный код состояния клиенту.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="fc7f4-178">Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="fc7f4-179">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="fc7f4-180">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="fc7f4-181">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="fc7f4-182">Обрабатывает запрос без перенаправления к другой конечной точке.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="fc7f4-183">Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="fc7f4-184">Сохраняет и возвращает исходный код состояния с ответом.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="fc7f4-185">Шаблоны URL-адреса и строки запроса могут содержать заполнитель (`{0}`) для кода состояния.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="fc7f4-186">Шаблон URL-адреса должен начинаться с косой черты (`/`).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="fc7f4-187">При использовании заполнителя в пути убедитесь, что конечная точка (страница или контроллер) могут обрабатывать сегмент пути.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="fc7f4-188">Например, страница Razor для ошибок должна принимать необязательное значение сегмента с директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="fc7f4-189">Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="fc7f4-190">Отключение страниц с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="fc7f4-190">Disable status code pages</span></span>

<span data-ttu-id="fc7f4-191">Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="fc7f4-192">Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="fc7f4-193">Код обработки исключений</span><span class="sxs-lookup"><span data-stu-id="fc7f4-193">Exception-handling code</span></span>

<span data-ttu-id="fc7f4-194">Код на страницах обработки исключений может создавать исключения.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="fc7f4-195">Часто желательно, чтобы страницы ошибок в рабочей среде содержали только статическое содержимое.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="fc7f4-196">Заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="fc7f4-196">Response headers</span></span>

<span data-ttu-id="fc7f4-197">После отправки заголовков для ответа происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="fc7f4-198">Приложение не может изменить код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="fc7f4-199">Нельзя запустить страницу или обработчик исключений.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="fc7f4-200">Необходимо завершить ответ или прервать подключение.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="fc7f4-201">Обработка исключений на сервере</span><span class="sxs-lookup"><span data-stu-id="fc7f4-201">Server exception handling</span></span>

<span data-ttu-id="fc7f4-202">Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) может выполнять ряд операций по обработке исключений.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="fc7f4-203">Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ *500 Internal Server Error* (внутренняя ошибка сервера) без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="fc7f4-204">Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="fc7f4-205">Запросы, не обработанные приложением, обрабатываются сервером.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="fc7f4-206">Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="fc7f4-207">Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="fc7f4-208">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="fc7f4-208">Startup exception handling</span></span>

<span data-ttu-id="fc7f4-209">Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="fc7f4-210">Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="fc7f4-211">На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="fc7f4-212">При сбое привязки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="fc7f4-212">If binding fails:</span></span>

* <span data-ttu-id="fc7f4-213">На уровне размещения в журнале фиксируется критическое исключение.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="fc7f4-214">Выполняется аварийное завершение процесса dotnet.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="fc7f4-215">Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="fc7f4-216">При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="fc7f4-217">Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="fc7f4-218">Страница ошибок базы данных</span><span class="sxs-lookup"><span data-stu-id="fc7f4-218">Database error page</span></span>

<span data-ttu-id="fc7f4-219">ПО промежуточного слоя для отображения страницы ошибок базы данных перехватывает исключения, относящиеся к базе данных, которые могут быть устранены при помощи миграций Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="fc7f4-220">При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="fc7f4-221">Эту страницу следует включать только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="fc7f4-222">Включите страницу, добавив код в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="fc7f4-223">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="fc7f4-223">Exception filters</span></span>

<span data-ttu-id="fc7f4-224">В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="fc7f4-225">В приложениях Razor Pages они могут быть настроены глобально или для модели страницы.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="fc7f4-226">Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="fc7f4-227">Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="fc7f4-228">Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Но эти фильтры не так гибки, как ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="fc7f4-229">Мы рекомендуем использовать ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-229">We recommend using the middleware.</span></span> <span data-ttu-id="fc7f4-230">Используйте фильтры, только если ошибки нужно обрабатывать по-разному в зависимости от выбранного действия MVC.</span><span class="sxs-lookup"><span data-stu-id="fc7f4-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="fc7f4-231">Ошибки состояния модели</span><span class="sxs-lookup"><span data-stu-id="fc7f4-231">Model state errors</span></span>

<span data-ttu-id="fc7f4-232">Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="fc7f4-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc7f4-233">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fc7f4-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
