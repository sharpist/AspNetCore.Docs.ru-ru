---
title: Обработка ошибок в ASP.NET Core
author: rick-anderson
description: Узнайте, как обрабатывать ошибки в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060473"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="b3116-103">Обработка ошибок в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3116-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b3116-104">Авторы: [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin), [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="b3116-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b3116-105">В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3116-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b3116-106">Для веб-API см. раздел <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="b3116-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b3116-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3116-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b3116-108">См. раздел [Загрузка примера](xref:index#how-to-download-a-sample). Вкладка "Сеть" в средствах разработчика F12 в браузере полезна при тестировании примера приложения.</span><span class="sxs-lookup"><span data-stu-id="b3116-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b3116-109">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="b3116-109">Developer Exception Page</span></span>

<span data-ttu-id="b3116-110">*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов.</span><span class="sxs-lookup"><span data-stu-id="b3116-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b3116-111">Шаблоны ASP.NET Core создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="b3116-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="b3116-112">Выделенный выше код включает страницу исключений для разработчика при выполнении приложения в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b3116-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b3116-113">Шаблоны помещают метод <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> в начале конвейера ПО промежуточного слоя, чтобы он мог перехватывать исключения, вызываемые в последующем ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="b3116-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="b3116-114">Приведенный выше код включает страницу исключений для разработчика \* **только** _ при выполнении приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="b3116-114">The preceding code enables the Developer Exception Page \* **only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="b3116-115">Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="b3116-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="b3116-116">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b3116-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b3116-117">Страница исключений для разработчика содержит следующие сведения об исключении и запросе:</span><span class="sxs-lookup"><span data-stu-id="b3116-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="b3116-118">_ Трассировка стека</span><span class="sxs-lookup"><span data-stu-id="b3116-118">_ Stack trace</span></span>
* <span data-ttu-id="b3116-119">параметры строки запроса (при наличии);</span><span class="sxs-lookup"><span data-stu-id="b3116-119">Query string parameters if any</span></span>
* <span data-ttu-id="b3116-120">Cookie (при наличии);</span><span class="sxs-lookup"><span data-stu-id="b3116-120">Cookies if any</span></span>
* <span data-ttu-id="b3116-121">Заголовки</span><span class="sxs-lookup"><span data-stu-id="b3116-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b3116-122">Страница обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-122">Exception handler page</span></span>

<span data-ttu-id="b3116-123">Чтобы настроить пользовательскую страницу обработки ошибок для [рабочей среды](xref:fundamentals/environments), вызовите <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="b3116-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3116-124">Это ПО промежуточного слоя для обработки исключений выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b3116-124">This exception handling middleware:</span></span>

* <span data-ttu-id="b3116-125">Перехватывает и записывает в журнал исключения.</span><span class="sxs-lookup"><span data-stu-id="b3116-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b3116-126">повторно выполняет запрос в альтернативном конвейере по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="b3116-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="b3116-127">Запрос не выполняется повторно, если запущен отклик.</span><span class="sxs-lookup"><span data-stu-id="b3116-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b3116-128">Созданный шаблоном код повторно выполняет запрос, используя путь `/Error`.</span><span class="sxs-lookup"><span data-stu-id="b3116-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="b3116-129">В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки:</span><span class="sxs-lookup"><span data-stu-id="b3116-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b3116-130">Шаблон приложения Razor Pages предоставляет страницу ошибки ( *CSHTML* ) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b3116-130">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b3116-131">Для приложения MVC шаблон проекта содержит метод действия `Error` и представление ошибок для контроллера Home.</span><span class="sxs-lookup"><span data-stu-id="b3116-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="b3116-132">Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="b3116-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="b3116-133">Из-за использования явных команд некоторые запросы могут не передаваться в метод действия.</span><span class="sxs-lookup"><span data-stu-id="b3116-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="b3116-134">Разрешите анонимный доступ к методу, если не прошедшие проверку подлинности пользователи должны видеть представление ошибок.</span><span class="sxs-lookup"><span data-stu-id="b3116-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b3116-135">Доступ к исключению</span><span class="sxs-lookup"><span data-stu-id="b3116-135">Access the exception</span></span>

<span data-ttu-id="b3116-136">Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в обработчике ошибок.</span><span class="sxs-lookup"><span data-stu-id="b3116-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="b3116-137">Следующий код добавляет `ExceptionMessage` в файл *Pages/Error.cshtml.cs* по умолчанию, создаваемый шаблонами ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3116-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="b3116-138">**Не** передавайте клиентам конфиденциальную информацию об ошибках.</span><span class="sxs-lookup"><span data-stu-id="b3116-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b3116-139">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="b3116-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3116-140">Чтобы протестировать исключение в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="b3116-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3116-141">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="b3116-141">Set the environment to production.</span></span>
* <span data-ttu-id="b3116-142">Удалите комментарии из `webBuilder.UseStartup<Startup>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3116-143">На домашней странице выберите **Вызвать исключение**.</span><span class="sxs-lookup"><span data-stu-id="b3116-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b3116-144">Лямбда-функция для обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-144">Exception handler lambda</span></span>

<span data-ttu-id="b3116-145">Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="b3116-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3116-146">Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b3116-147">В следующем коде для обработки исключений используется лямбда-выражение:</span><span class="sxs-lookup"><span data-stu-id="b3116-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="b3116-148">**Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>.</span><span class="sxs-lookup"><span data-stu-id="b3116-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b3116-149">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="b3116-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3116-150">Чтобы протестировать лямбда-выражение для обработки исключений в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="b3116-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3116-151">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="b3116-151">Set the environment to production.</span></span>
* <span data-ttu-id="b3116-152">Удалите комментарии из `webBuilder.UseStartup<StartupLambda>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3116-153">На домашней странице выберите **Вызвать исключение**.</span><span class="sxs-lookup"><span data-stu-id="b3116-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b3116-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b3116-154">UseStatusCodePages</span></span>

<span data-ttu-id="b3116-155">По умолчанию приложение ASP.NET Core не предоставляет страницу для кодов состояния ошибок HTTP, таких как код *404 Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="b3116-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b3116-156">Когда в приложении возникает ошибка HTTP 400–499 без текста, возвращается код состояния и пустой текст ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="b3116-157">Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="b3116-158">Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b3116-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="b3116-159">Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="b3116-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="b3116-160">Например, вызовите `UseStatusCodePages` до ПО промежуточного слоя для статических файлов и конечных точек.</span><span class="sxs-lookup"><span data-stu-id="b3116-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="b3116-161">Если `UseStatusCodePages` не используется, при переходе по URL-адресу без конечной точки возвращается зависящее от браузера сообщение об ошибке, в котором указывается, что конечная точка не найдена.</span><span class="sxs-lookup"><span data-stu-id="b3116-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b3116-162">Например, можно перейти по следующему адресу: `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="b3116-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b3116-163">В этом случае при вызове `UseStatusCodePages` браузер вернет следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="b3116-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="b3116-164">`UseStatusCodePages` обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="b3116-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3116-165">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="b3116-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3116-166">Настройте среду как рабочую.</span><span class="sxs-lookup"><span data-stu-id="b3116-166">Set the environment to production.</span></span>
* <span data-ttu-id="b3116-167">Удалите комментарии из `webBuilder.UseStartup<StartupUseStatusCodePages>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3116-168">Выберите ссылки на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="b3116-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b3116-169">UseStatusCodePages со строкой формата</span><span class="sxs-lookup"><span data-stu-id="b3116-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b3116-170">Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает тип содержимого и строку формата.</span><span class="sxs-lookup"><span data-stu-id="b3116-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="b3116-171">В предыдущем коде `{0}` является заполнителем для кода ошибки.</span><span class="sxs-lookup"><span data-stu-id="b3116-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="b3116-172">`UseStatusCodePages` со строкой формата обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="b3116-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3116-173">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupFormat>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b3116-174">UseStatusCodePages с лямбда-выражением</span><span class="sxs-lookup"><span data-stu-id="b3116-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b3116-175">Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает лямбда-выражение.</span><span class="sxs-lookup"><span data-stu-id="b3116-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="b3116-176">`UseStatusCodePages` с лямбда-выражением обычно не применяется в рабочей среде, так как возвращает сообщение, бесполезное для пользователей.</span><span class="sxs-lookup"><span data-stu-id="b3116-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3116-177">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupStatusLambda>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b3116-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="b3116-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b3116-179">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="b3116-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b3116-180">Отправляет клиенту код состояния [302 — Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302).</span><span class="sxs-lookup"><span data-stu-id="b3116-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="b3116-181">Перенаправляет клиент в конечную точку обработки ошибок, указанную в шаблоне URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="b3116-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="b3116-182">Конечная точка обработки ошибок обычно отображает сведения об ошибке и возвращает код HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="b3116-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="b3116-183">Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в предыдущем коде.</span><span class="sxs-lookup"><span data-stu-id="b3116-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="b3116-184">Если шаблон URL-адреса начинается с символа `~` (тильды), `~` заменяется `PathBase` приложения.</span><span class="sxs-lookup"><span data-stu-id="b3116-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b3116-185">При указании конечной точки в приложении создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="b3116-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3116-186">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="b3116-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3116-187">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="b3116-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b3116-188">Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку.</span><span class="sxs-lookup"><span data-stu-id="b3116-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b3116-189">Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b3116-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b3116-190">Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b3116-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="b3116-191">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupSCredirect>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b3116-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b3116-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b3116-193">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="b3116-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b3116-194">Возвращает исходный код состояния клиенту.</span><span class="sxs-lookup"><span data-stu-id="b3116-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b3116-195">Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.</span><span class="sxs-lookup"><span data-stu-id="b3116-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="b3116-196">Если указывается конечная точка в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="b3116-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3116-197">Обязательно поместите `UseStatusCodePagesWithReExecute` перед `UseRouting`, чтобы запрос можно было перенаправить на страницу состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b3116-198">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="b3116-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3116-199">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="b3116-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b3116-200">Обрабатывает запрос без перенаправления к другой конечной точке.</span><span class="sxs-lookup"><span data-stu-id="b3116-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b3116-201">Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.</span><span class="sxs-lookup"><span data-stu-id="b3116-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b3116-202">Сохраняет и возвращает исходный код состояния с ответом.</span><span class="sxs-lookup"><span data-stu-id="b3116-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b3116-203">Шаблоны URL-адреса и строки запроса могут содержать заполнитель `{0}` для кода состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="b3116-204">Шаблон URL-адреса должен начинаться с символа `/`.</span><span class="sxs-lookup"><span data-stu-id="b3116-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="b3116-205">Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b3116-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="b3116-206">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) в файле [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages).</span><span class="sxs-lookup"><span data-stu-id="b3116-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3116-207">Чтобы протестировать `UseStatusCodePages` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), удалите комментарии из `webBuilder.UseStartup<StartupSCreX>();` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3116-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="b3116-208">Отключение страниц с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="b3116-208">Disable status code pages</span></span>

<span data-ttu-id="b3116-209">Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="b3116-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b3116-210">Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.</span><span class="sxs-lookup"><span data-stu-id="b3116-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="b3116-211">Код обработки исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-211">Exception-handling code</span></span>

<span data-ttu-id="b3116-212">Код на страницах обработки исключений также может создавать исключения.</span><span class="sxs-lookup"><span data-stu-id="b3116-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="b3116-213">Рабочие страницы ошибок необходимо тщательно тестировать, чтобы они не создавали собственных исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="b3116-214">Заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="b3116-214">Response headers</span></span>

<span data-ttu-id="b3116-215">После отправки заголовков для ответа происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="b3116-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b3116-216">Приложение не может изменить код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b3116-217">Нельзя запустить страницу или обработчик исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b3116-218">Необходимо завершить ответ или прервать подключение.</span><span class="sxs-lookup"><span data-stu-id="b3116-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b3116-219">Обработка исключений на сервере</span><span class="sxs-lookup"><span data-stu-id="b3116-219">Server exception handling</span></span>

<span data-ttu-id="b3116-220">Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) также может обрабатывать некоторые исключения.</span><span class="sxs-lookup"><span data-stu-id="b3116-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b3116-221">Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ `500 - Internal Server Error` (внутренняя ошибка сервера) без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="b3116-222">Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение.</span><span class="sxs-lookup"><span data-stu-id="b3116-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b3116-223">Запросы, не обработанные приложением, обрабатываются сервером.</span><span class="sxs-lookup"><span data-stu-id="b3116-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="b3116-224">Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере.</span><span class="sxs-lookup"><span data-stu-id="b3116-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b3116-225">Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.</span><span class="sxs-lookup"><span data-stu-id="b3116-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b3116-226">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="b3116-226">Startup exception handling</span></span>

<span data-ttu-id="b3116-227">Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения.</span><span class="sxs-lookup"><span data-stu-id="b3116-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b3116-228">Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="b3116-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b3116-229">На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла.</span><span class="sxs-lookup"><span data-stu-id="b3116-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b3116-230">При сбое привязки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="b3116-230">If binding fails:</span></span>

* <span data-ttu-id="b3116-231">На уровне размещения в журнале фиксируется критическое исключение.</span><span class="sxs-lookup"><span data-stu-id="b3116-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b3116-232">Выполняется аварийное завершение процесса dotnet.</span><span class="sxs-lookup"><span data-stu-id="b3116-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="b3116-233">Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.</span><span class="sxs-lookup"><span data-stu-id="b3116-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b3116-234">При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить.</span><span class="sxs-lookup"><span data-stu-id="b3116-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b3116-235">Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b3116-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b3116-236">Страница ошибок базы данных</span><span class="sxs-lookup"><span data-stu-id="b3116-236">Database error page</span></span>

<span data-ttu-id="b3116-237">Фильтр исключений для страницы разработчика базы данных `AddDatabaseDeveloperPageExceptionFilter` перехватывает исключения, относящиеся к базе данных, которые могут быть устранены с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b3116-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="b3116-238">При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="b3116-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="b3116-239">Эта страница включается только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="b3116-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="b3116-240">Следующий код был создан шаблонами страниц Razor в ASP.NET Core при указании отдельных учетных записей пользователей:</span><span class="sxs-lookup"><span data-stu-id="b3116-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="b3116-241">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-241">Exception filters</span></span>

<span data-ttu-id="b3116-242">В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий.</span><span class="sxs-lookup"><span data-stu-id="b3116-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b3116-243">В приложениях Razor Pages они могут быть настроены глобально или для модели страницы.</span><span class="sxs-lookup"><span data-stu-id="b3116-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b3116-244">Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра.</span><span class="sxs-lookup"><span data-stu-id="b3116-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b3116-245">Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b3116-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="b3116-246">Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Однако эти фильтры не так гибки, как встроенное [ПО промежуточного слоя для обработки исключений](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="b3116-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="b3116-247">Мы рекомендуем использовать `UseExceptionHandler`, если ошибки не нужно обрабатывать по-разному в зависимости от выбранного действия MVC.</span><span class="sxs-lookup"><span data-stu-id="b3116-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="b3116-248">Ошибки состояния модели</span><span class="sxs-lookup"><span data-stu-id="b3116-248">Model state errors</span></span>

<span data-ttu-id="b3116-249">Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b3116-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3116-250">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b3116-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b3116-251">Авторы: [Том Дикстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="b3116-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b3116-252">В этой статье рассматриваются основные методы обработки ошибок в веб-приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3116-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b3116-253">Для веб-API см. раздел <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="b3116-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b3116-254">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3116-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b3116-255">См. раздел [Загрузка примера](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b3116-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b3116-256">Страница со сведениями об исключении для разработчика</span><span class="sxs-lookup"><span data-stu-id="b3116-256">Developer Exception Page</span></span>

<span data-ttu-id="b3116-257">*Страница исключений для разработчика* содержит подробные сведения об исключениях запросов.</span><span class="sxs-lookup"><span data-stu-id="b3116-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b3116-258">Шаблоны ASP.NET Core создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="b3116-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="b3116-259">Приведенный выше код включает страницу исключений для разработчика при выполнении приложения в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b3116-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b3116-260">Шаблоны помещают метод <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> перед всем ПО промежуточного слоя, чтобы исключения перехватывались в этом ПО.</span><span class="sxs-lookup"><span data-stu-id="b3116-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="b3116-261">Приведенный выше код включает страницу исключений для разработчика **только при выполнении приложения в среде разработки**.</span><span class="sxs-lookup"><span data-stu-id="b3116-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="b3116-262">Подробные сведения об исключениях не должны быть общедоступными при выполнении приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="b3116-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="b3116-263">Дополнительные сведения о настройке среды см. в статье <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b3116-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b3116-264">Страница исключений для разработчика содержит следующие сведения об исключении и запросе:</span><span class="sxs-lookup"><span data-stu-id="b3116-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="b3116-265">Трассировка стека</span><span class="sxs-lookup"><span data-stu-id="b3116-265">Stack trace</span></span>
* <span data-ttu-id="b3116-266">параметры строки запроса (при наличии);</span><span class="sxs-lookup"><span data-stu-id="b3116-266">Query string parameters if any</span></span>
* <span data-ttu-id="b3116-267">Cookie (при наличии);</span><span class="sxs-lookup"><span data-stu-id="b3116-267">Cookies if any</span></span>
* <span data-ttu-id="b3116-268">Заголовки</span><span class="sxs-lookup"><span data-stu-id="b3116-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b3116-269">Страница обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-269">Exception handler page</span></span>

<span data-ttu-id="b3116-270">Чтобы настроить пользовательскую страницу обработки ошибок для рабочей среды, используйте ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="b3116-271">ПО промежуточного слоя выполняет такие функции:</span><span class="sxs-lookup"><span data-stu-id="b3116-271">The middleware:</span></span>

* <span data-ttu-id="b3116-272">Перехватывает и записывает в журнал исключения.</span><span class="sxs-lookup"><span data-stu-id="b3116-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b3116-273">Повторно выполняет запрос в альтернативном конвейере для указанной страницы или контроллера.</span><span class="sxs-lookup"><span data-stu-id="b3116-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="b3116-274">Запрос не выполняется повторно, если запущен отклик.</span><span class="sxs-lookup"><span data-stu-id="b3116-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b3116-275">Созданный шаблоном код повторно выполняет запрос к `/Error`.</span><span class="sxs-lookup"><span data-stu-id="b3116-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="b3116-276">В следующем примере с помощью <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> добавляется ПО промежуточного слоя для обработки исключений в средах, не предназначенных для разработки.</span><span class="sxs-lookup"><span data-stu-id="b3116-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b3116-277">Шаблон приложения Razor Pages предоставляет страницу ошибки ( *CSHTML* ) и класс <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b3116-277">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b3116-278">Для приложения MVC шаблон проекта содержит метод действия при возникновении ошибки и представление ошибок для контроллера Home.</span><span class="sxs-lookup"><span data-stu-id="b3116-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="b3116-279">Не следует помечать метод действия обработки ошибок атрибутами метода HTTP, например `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="b3116-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="b3116-280">Из-за использования явных команд некоторые запросы могут не передаваться в метод.</span><span class="sxs-lookup"><span data-stu-id="b3116-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="b3116-281">Разрешите анонимный доступ к методу, если не прошедшие проверку подлинности пользователи должны видеть представление ошибок.</span><span class="sxs-lookup"><span data-stu-id="b3116-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b3116-282">Доступ к исключению</span><span class="sxs-lookup"><span data-stu-id="b3116-282">Access the exception</span></span>

<span data-ttu-id="b3116-283">Используйте интерфейс <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, чтобы получить доступ к исключению и к пути исходного запроса в контроллере или на странице обработчика ошибок:</span><span class="sxs-lookup"><span data-stu-id="b3116-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="b3116-284">**Не** передавайте клиентам конфиденциальную информацию об ошибках.</span><span class="sxs-lookup"><span data-stu-id="b3116-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b3116-285">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="b3116-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3116-286">Чтобы активировать предыдущую страницу обработки исключений, задайте рабочую среду и принудительно вызовите исключение.</span><span class="sxs-lookup"><span data-stu-id="b3116-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b3116-287">Лямбда-функция для обработчика исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-287">Exception handler lambda</span></span>

<span data-ttu-id="b3116-288">Альтернативой [пользовательской странице обработчика исключений](#exception-handler-page) является предоставление лямбда-функции для <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="b3116-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3116-289">Использование лямбда-функции позволяет получить доступ к ошибке до возврата ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b3116-290">Ниже приведен пример использования лямбда-функции для обработки исключений:</span><span class="sxs-lookup"><span data-stu-id="b3116-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="b3116-291">В приведенном выше коде добавляется `await context.Response.WriteAsync(new string(' ', 512));`, поэтому браузер Internet Explorer отображает сообщение об ошибке, а не сообщение об ошибке IE.</span><span class="sxs-lookup"><span data-stu-id="b3116-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="b3116-292">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="b3116-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="b3116-293">**Не** передавайте клиентам конфиденциальную информацию об ошибках из <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> или <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>.</span><span class="sxs-lookup"><span data-stu-id="b3116-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b3116-294">Сохранение ошибок создает риски для безопасности.</span><span class="sxs-lookup"><span data-stu-id="b3116-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3116-295">Чтобы просмотреть результат обработки ошибок лямбда-функцией в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), используйте директивы препроцессора `ProdEnvironment` и `ErrorHandlerLambda`, а на домашней странице выберите **Trigger an exception** (Вызывать исключение).</span><span class="sxs-lookup"><span data-stu-id="b3116-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b3116-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b3116-296">UseStatusCodePages</span></span>

<span data-ttu-id="b3116-297">По умолчанию приложение ASP.NET Core не предоставляет страницы для кодов состояния HTTP, таких как код *404 Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="b3116-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b3116-298">Приложение возвращает код состояния без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="b3116-299">Чтобы указать коды состояния, используйте ПО промежуточного слоя страниц с кодами состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="b3116-300">Это ПО промежуточного слоя доступно в пакете [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/).</span><span class="sxs-lookup"><span data-stu-id="b3116-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="b3116-301">Чтобы включить обработчики по умолчанию, возвращающие только текст для распространенных кодов состояния ошибки, вызовите <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b3116-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="b3116-302">Вызовите `UseStatusCodePages` до ПО промежуточного слоя для обработки запросов (например ПО промежуточного слоя для статических файлов и ПО промежуточного слоя MVC).</span><span class="sxs-lookup"><span data-stu-id="b3116-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="b3116-303">Если `UseStatusCodePages` не используется, при переходе по URL-адресу без конечной точки возвращается зависящее от браузера сообщение об ошибке, в котором указывается, что конечная точка не найдена.</span><span class="sxs-lookup"><span data-stu-id="b3116-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b3116-304">Например, можно перейти по следующему адресу: `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="b3116-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b3116-305">В этом случае при вызове `UseStatusCodePages` браузер вернет следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="b3116-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b3116-306">UseStatusCodePages со строкой формата</span><span class="sxs-lookup"><span data-stu-id="b3116-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b3116-307">Чтобы настроить тип содержимого и указать текст ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает тип содержимого и строку формата.</span><span class="sxs-lookup"><span data-stu-id="b3116-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b3116-308">UseStatusCodePages с лямбда-выражением</span><span class="sxs-lookup"><span data-stu-id="b3116-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b3116-309">Чтобы указать пользовательский код для обработки ошибок и отображения ответа, используйте перегрузку <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, которая принимает лямбда-выражение.</span><span class="sxs-lookup"><span data-stu-id="b3116-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b3116-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="b3116-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b3116-311">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="b3116-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b3116-312">Отправляет клиенту код состояния *302 — Found*.</span><span class="sxs-lookup"><span data-stu-id="b3116-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="b3116-313">Перенаправляет клиента к расположению, предоставленному в шаблоне URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="b3116-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="b3116-314">Шаблон URL-адреса может содержать заполнитель `{0}` для кода состояния, как показано в примере.</span><span class="sxs-lookup"><span data-stu-id="b3116-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="b3116-315">Если шаблон URL-адреса начинается с символа `~` (тильды), `~` заменяется `PathBase` приложения.</span><span class="sxs-lookup"><span data-stu-id="b3116-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b3116-316">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="b3116-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3116-317">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b3116-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b3116-318">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="b3116-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b3116-319">Должно перенаправлять клиент в другую конечную точку, что обычно бывает в случаях, когда другое приложение обрабатывает ошибку.</span><span class="sxs-lookup"><span data-stu-id="b3116-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b3116-320">Для веб-приложений в адресной строке браузера клиента отображается конечная точка перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b3116-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b3116-321">Не должно сохранять и возвращать исходный код состояния с ответом первичного перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b3116-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b3116-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b3116-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b3116-323">Метод расширения <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="b3116-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b3116-324">Возвращает исходный код состояния клиенту.</span><span class="sxs-lookup"><span data-stu-id="b3116-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b3116-325">Позволяет создать текст ответа путем повторного выполнения конвейера запросов с использованием другого пути.</span><span class="sxs-lookup"><span data-stu-id="b3116-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="b3116-326">Если вы указываете на конечную точку в приложении, создайте представление MVC или страницу Razor для конечной точки.</span><span class="sxs-lookup"><span data-stu-id="b3116-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3116-327">Обязательно поместите `UseStatusCodePagesWithReExecute` перед `UseRouting`, чтобы запрос можно было перенаправить на страницу состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b3116-328">Пример Razor Pages доступен в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) в файле *Pages/StatusCode.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b3116-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b3116-329">Этот метод обычно используется, если приложение:</span><span class="sxs-lookup"><span data-stu-id="b3116-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b3116-330">Обрабатывает запрос без перенаправления к другой конечной точке.</span><span class="sxs-lookup"><span data-stu-id="b3116-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b3116-331">Для веб-приложений в адресной строке браузера клиента отображается изначально запрошенная конечная точка.</span><span class="sxs-lookup"><span data-stu-id="b3116-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b3116-332">Сохраняет и возвращает исходный код состояния с ответом.</span><span class="sxs-lookup"><span data-stu-id="b3116-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b3116-333">Шаблоны URL-адреса и строки запроса могут содержать заполнитель (`{0}`) для кода состояния.</span><span class="sxs-lookup"><span data-stu-id="b3116-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="b3116-334">Шаблон URL-адреса должен начинаться с косой черты (`/`).</span><span class="sxs-lookup"><span data-stu-id="b3116-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="b3116-335">При использовании заполнителя в пути убедитесь, что конечная точка (страница или контроллер) могут обрабатывать сегмент пути.</span><span class="sxs-lookup"><span data-stu-id="b3116-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="b3116-336">Например, страница Razor для ошибок должна принимать необязательное значение сегмента с директивой `@page`.</span><span class="sxs-lookup"><span data-stu-id="b3116-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="b3116-337">Конечная точка, которая обрабатывает ошибку, может получать исходный URL-адрес, вызвавший ошибку, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b3116-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="b3116-338">Отключение страниц с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="b3116-338">Disable status code pages</span></span>

<span data-ttu-id="b3116-339">Чтобы отключить страницы кодов состояния для метода контроллера или действия MVC, используйте атрибут [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="b3116-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b3116-340">Чтобы отключить страницы кодов состояния для конкретных запросов в методе обработчика Razor Pages или в контроллере MVC, используйте <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>.</span><span class="sxs-lookup"><span data-stu-id="b3116-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="b3116-341">Код обработки исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-341">Exception-handling code</span></span>

<span data-ttu-id="b3116-342">Код на страницах обработки исключений может создавать исключения.</span><span class="sxs-lookup"><span data-stu-id="b3116-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="b3116-343">Часто желательно, чтобы страницы ошибок в рабочей среде содержали только статическое содержимое.</span><span class="sxs-lookup"><span data-stu-id="b3116-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="b3116-344">Заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="b3116-344">Response headers</span></span>

<span data-ttu-id="b3116-345">После отправки заголовков для ответа происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="b3116-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b3116-346">Приложение не может изменить код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b3116-347">Нельзя запустить страницу или обработчик исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b3116-348">Необходимо завершить ответ или прервать подключение.</span><span class="sxs-lookup"><span data-stu-id="b3116-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b3116-349">Обработка исключений на сервере</span><span class="sxs-lookup"><span data-stu-id="b3116-349">Server exception handling</span></span>

<span data-ttu-id="b3116-350">Помимо логики обработки исключений в приложении, [реализация HTTP-сервера](xref:fundamentals/servers/index) может выполнять ряд операций по обработке исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b3116-351">Если сервер перехватывает исключение перед отправкой заголовков ответа, он отсылает ответ *500 Internal Server Error* (внутренняя ошибка сервера) без текста ответа.</span><span class="sxs-lookup"><span data-stu-id="b3116-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="b3116-352">Если сервер перехватывает исключение после отправки заголовков ответа, он закрывает соединение.</span><span class="sxs-lookup"><span data-stu-id="b3116-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b3116-353">Запросы, не обработанные приложением, обрабатываются сервером.</span><span class="sxs-lookup"><span data-stu-id="b3116-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="b3116-354">Все исключения, возникшие при обработке запроса серверов, обрабатываются с помощью механизма обработки исключений на сервере.</span><span class="sxs-lookup"><span data-stu-id="b3116-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b3116-355">Пользовательские страницы ошибок приложений, ПО промежуточного слоя для обработки исключений и фильтры не влияют на этот процесс.</span><span class="sxs-lookup"><span data-stu-id="b3116-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b3116-356">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="b3116-356">Startup exception handling</span></span>

<span data-ttu-id="b3116-357">Исключения, возникающие во время запуска приложения, могут обрабатываться только на уровне размещения.</span><span class="sxs-lookup"><span data-stu-id="b3116-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b3116-358">Узел можно настроить для [перехвата ошибок при загрузке](xref:fundamentals/host/web-host#capture-startup-errors) и [записи подробных сведений об ошибках](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="b3116-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b3116-359">На уровне размещения может отображаться страница со сведениями о перехваченной ошибке при загрузке, только если ошибка произошла после привязки адреса и порта узла.</span><span class="sxs-lookup"><span data-stu-id="b3116-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b3116-360">При сбое привязки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="b3116-360">If binding fails:</span></span>

* <span data-ttu-id="b3116-361">На уровне размещения в журнале фиксируется критическое исключение.</span><span class="sxs-lookup"><span data-stu-id="b3116-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b3116-362">Выполняется аварийное завершение процесса dotnet.</span><span class="sxs-lookup"><span data-stu-id="b3116-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="b3116-363">Если приложение запущено на HTTP-сервере [Kestrel](xref:fundamentals/servers/kestrel), страница со сведениями об ошибке не выводится.</span><span class="sxs-lookup"><span data-stu-id="b3116-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b3116-364">При работе в службах [IIS](/iis) (или Службе приложений Azure) либо [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)[модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) возвращает ошибку *502.5 Process Failure* (ошибка процесса), если процесс невозможно запустить.</span><span class="sxs-lookup"><span data-stu-id="b3116-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b3116-365">Для получения дополнительной информации см. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b3116-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b3116-366">Страница ошибок базы данных</span><span class="sxs-lookup"><span data-stu-id="b3116-366">Database error page</span></span>

<span data-ttu-id="b3116-367">ПО промежуточного слоя для отображения страницы ошибок базы данных перехватывает исключения, относящиеся к базе данных, которые могут быть устранены при помощи миграций Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b3116-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="b3116-368">При возникновении этих исключений формируется HTML-ответ с подробными сведениями о возможных действиях для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="b3116-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="b3116-369">Эту страницу следует включать только в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="b3116-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="b3116-370">Включите страницу, добавив код в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b3116-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="b3116-371">Для <xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> необходим пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="b3116-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="b3116-372">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="b3116-372">Exception filters</span></span>

<span data-ttu-id="b3116-373">В приложениях MVC фильтры исключений можно настраивать как глобально, так и для отдельных контроллеров или действий.</span><span class="sxs-lookup"><span data-stu-id="b3116-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b3116-374">В приложениях Razor Pages они могут быть настроены глобально или для модели страницы.</span><span class="sxs-lookup"><span data-stu-id="b3116-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b3116-375">Эти фильтры обрабатывают все необработанные исключения, которые возникают во время выполнения действия контроллера или другого фильтра.</span><span class="sxs-lookup"><span data-stu-id="b3116-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b3116-376">Для получения дополнительной информации см. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b3116-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="b3116-377">Фильтры исключений полезны при перехвате исключений, которые возникают в действиях MVC. Но эти фильтры не так гибки, как ПО промежуточного слоя для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="b3116-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="b3116-378">Мы рекомендуем использовать ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="b3116-378">We recommend using the middleware.</span></span> <span data-ttu-id="b3116-379">Используйте фильтры, только если ошибки нужно обрабатывать по-разному в зависимости от выбранного действия MVC.</span><span class="sxs-lookup"><span data-stu-id="b3116-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="b3116-380">Ошибки состояния модели</span><span class="sxs-lookup"><span data-stu-id="b3116-380">Model state errors</span></span>

<span data-ttu-id="b3116-381">Сведения о том, как обрабатывать ошибки состояния модели, см. в статьях о [привязке модели](xref:mvc/models/model-binding) и [проверке модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b3116-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3116-382">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b3116-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
