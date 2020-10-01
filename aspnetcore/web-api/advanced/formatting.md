---
title: Форматирование данных отклика в веб-API ASP.NET Core
author: ardalis
description: Сведения о форматировании данных отклика в веб-API ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: b89be93fc33d1eba5c2ad9508adf93fa54014ff8
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606787"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="d7404-103">Форматирование данных отклика в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7404-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="d7404-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="d7404-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d7404-105">Приложение MVC ASP.NET Core поддерживает форматирование данных ответа.</span><span class="sxs-lookup"><span data-stu-id="d7404-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="d7404-106">Данные ответа могут возвращаться в определенных форматах или в формате, запрошенном клиентом.</span><span class="sxs-lookup"><span data-stu-id="d7404-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="d7404-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7404-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="d7404-108">Результаты действий для конкретного формата</span><span class="sxs-lookup"><span data-stu-id="d7404-108">Format-specific Action Results</span></span>

<span data-ttu-id="d7404-109">Некоторые типы результатов действий характерны для определенного формата, например <xref:Microsoft.AspNetCore.Mvc.JsonResult> и <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="d7404-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="d7404-110">Действия могут возвращать результаты в определенном формате независимо от настроек клиента.</span><span class="sxs-lookup"><span data-stu-id="d7404-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="d7404-111">Например, при возврате `JsonResult` возвращаются данные в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="d7404-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="d7404-112">При возврате `ContentResult` или строки возвращаются строковые данные в формате обычного текста.</span><span class="sxs-lookup"><span data-stu-id="d7404-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="d7404-113">Действие не должно возвращать данные конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="d7404-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="d7404-114">ASP.NET Core поддерживает любое возвращаемое значение объекта.</span><span class="sxs-lookup"><span data-stu-id="d7404-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="d7404-115">Результаты из действий, возвращающих объекты, которые не являются типами <xref:Microsoft.AspNetCore.Mvc.IActionResult>, сериализуются с помощью соответствующей реализации <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="d7404-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="d7404-116">Для получения дополнительной информации см. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="d7404-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="d7404-117">Встроенный вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> возвращает данные в формате JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="d7404-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="d7404-118">Скачанный пример возвращает список авторов.</span><span class="sxs-lookup"><span data-stu-id="d7404-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="d7404-119">При использовании средств разработчика в браузере (F12) или [Postman](https://www.getpostman.com/tools) с предыдущим кодом:</span><span class="sxs-lookup"><span data-stu-id="d7404-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="d7404-120">Отобразится заголовок ответа, содержащий **content-type:** `application/json; charset=utf-8`.</span><span class="sxs-lookup"><span data-stu-id="d7404-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="d7404-121">Отобразятся заголовки запросов.</span><span class="sxs-lookup"><span data-stu-id="d7404-121">The request headers are displayed.</span></span> <span data-ttu-id="d7404-122">Например, заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="d7404-122">For example, the `Accept` header.</span></span> <span data-ttu-id="d7404-123">Приведенный выше код игнорирует заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="d7404-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="d7404-124">Чтобы возвратить данные в формате обычного текста, используйте <xref:Microsoft.AspNetCore.Mvc.ContentResult> и вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:</span><span class="sxs-lookup"><span data-stu-id="d7404-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="d7404-125">В приведенном выше коде возвращаемым `Content-Type` является `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="d7404-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="d7404-126">При возврате строки возвращается `Content-Type` со значением `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="d7404-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="d7404-127">Для действий с несколькими возвращаемыми типами возвращается значение `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="d7404-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="d7404-128">Например, возвращаются различные коды состояния HTTP на основе результатов выполненных операций.</span><span class="sxs-lookup"><span data-stu-id="d7404-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="d7404-129">Согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="d7404-129">Content negotiation</span></span>

<span data-ttu-id="d7404-130">Согласование содержимого происходит, когда клиент задает [заголовок Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="d7404-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="d7404-131">Для ASP.NET Core по умолчанию используется формат [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="d7404-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="d7404-132">Согласование содержимого:</span><span class="sxs-lookup"><span data-stu-id="d7404-132">Content negotiation is:</span></span>

* <span data-ttu-id="d7404-133">реализуется с помощью <xref:Microsoft.AspNetCore.Mvc.ObjectResult>;</span><span class="sxs-lookup"><span data-stu-id="d7404-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="d7404-134">встроено в результаты действия с определенным кодом состояния, возвращаемые из вспомогательных методов;</span><span class="sxs-lookup"><span data-stu-id="d7404-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="d7404-135">вспомогательные методы результатов действия основаны на `ObjectResult`;</span><span class="sxs-lookup"><span data-stu-id="d7404-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="d7404-136">при возврате типа модели возвращаемым типом является `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="d7404-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="d7404-137">Следующий метод действия использует вспомогательные методы `Ok` и `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="d7404-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="d7404-138">По умолчанию ASP.NET Core поддерживает типы мультимедиа `application/json`, `text/json` и `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="d7404-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="d7404-139">Средства, такие как [Fiddler](https://www.telerik.com/fiddler) или [Postman](https://www.getpostman.com/tools), могут установить заголовок запроса `Accept`, чтобы указать формат возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="d7404-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="d7404-140">Если заголовок `Accept` содержит тип, который поддерживается сервером, возвращается этот тип.</span><span class="sxs-lookup"><span data-stu-id="d7404-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="d7404-141">Сведения о добавлении дополнительных форматировщиков приведены в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="d7404-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="d7404-142">Действия контроллера могут возвращать POCO (простые объекты CLR).</span><span class="sxs-lookup"><span data-stu-id="d7404-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="d7404-143">При возвращении POCO среда выполнения автоматически создает `ObjectResult`, который генерирует оболочку объекта.</span><span class="sxs-lookup"><span data-stu-id="d7404-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="d7404-144">Клиент получает отформатированный сериализованный объект.</span><span class="sxs-lookup"><span data-stu-id="d7404-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="d7404-145">Если возвращаемый объект имеет значение `null`, возвращается ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="d7404-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="d7404-146">Возвращение типа объекта:</span><span class="sxs-lookup"><span data-stu-id="d7404-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="d7404-147">В приведенном выше коде запрос допустимого псевдонима автора получает ответ `200 OK` с данными об авторе.</span><span class="sxs-lookup"><span data-stu-id="d7404-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="d7404-148">Запрос недопустимого псевдонима возвращает ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="d7404-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="d7404-149">Заголовок Accept</span><span class="sxs-lookup"><span data-stu-id="d7404-149">The Accept header</span></span>

<span data-ttu-id="d7404-150">*Согласование* содержимого выполняется только при наличии в запросе заголовка `Accept`.</span><span class="sxs-lookup"><span data-stu-id="d7404-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="d7404-151">Если запрос содержит заголовок Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d7404-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="d7404-152">перечисляет типы мультимедиа в заголовке Accept в порядке предпочтения;</span><span class="sxs-lookup"><span data-stu-id="d7404-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="d7404-153">пытается найти форматировщик, который может создать ответ в одном из указанных форматов.</span><span class="sxs-lookup"><span data-stu-id="d7404-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="d7404-154">Если форматировщик, который может удовлетворить запрос клиента, не найден, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d7404-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="d7404-155">возвращает значение `406 Not Acceptable`, если <xref:Microsoft.AspNetCore.Mvc.MvcOptions> задано, или:</span><span class="sxs-lookup"><span data-stu-id="d7404-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="d7404-156">пытается найти первый форматировщик, который может создать ответ.</span><span class="sxs-lookup"><span data-stu-id="d7404-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="d7404-157">Если форматировщик, обеспечивающий требуемый формат, не настроен, используется первый форматировщик, способный отформатировать данный объект.</span><span class="sxs-lookup"><span data-stu-id="d7404-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="d7404-158">Если в запросе не отображается заголовок `Accept`:</span><span class="sxs-lookup"><span data-stu-id="d7404-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="d7404-159">Для сериализации ответа используется первый форматировщик, который может работать с объектом.</span><span class="sxs-lookup"><span data-stu-id="d7404-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="d7404-160">Согласование не выполняется.</span><span class="sxs-lookup"><span data-stu-id="d7404-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="d7404-161">Сервер определяет возвращаемый формат.</span><span class="sxs-lookup"><span data-stu-id="d7404-161">The server is determining what format to return.</span></span>

<span data-ttu-id="d7404-162">Если заголовок Accept содержит `*/*`, он игнорируется, если только `RespectBrowserAcceptHeader` не имеет значение true в <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="d7404-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="d7404-163">Браузеры и согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="d7404-163">Browsers and content negotiation</span></span>

<span data-ttu-id="d7404-164">В отличие от обычных клиентов API, веб-браузеры предоставляют заголовки `Accept`.</span><span class="sxs-lookup"><span data-stu-id="d7404-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="d7404-165">Веб-браузер определяет множество форматов, включая подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="d7404-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="d7404-166">Если платформа обнаруживает, что запрос поступает из браузера, по умолчанию выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="d7404-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="d7404-167">Заголовок `Accept` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="d7404-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="d7404-168">Содержимое возвращается в формате JSON, если не указано иначе.</span><span class="sxs-lookup"><span data-stu-id="d7404-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="d7404-169">Это обеспечивает более согласованное взаимодействие между браузерами при использовании API.</span><span class="sxs-lookup"><span data-stu-id="d7404-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="d7404-170">Чтобы настроить приложение для учета заголовков Accept в браузере, установите для параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> значение `true`:</span><span class="sxs-lookup"><span data-stu-id="d7404-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="d7404-171">Настройка форматировщиков</span><span class="sxs-lookup"><span data-stu-id="d7404-171">Configure formatters</span></span>

<span data-ttu-id="d7404-172">В приложениях, которым требуется поддержка дополнительных форматов, можно добавлять соответствующие пакеты NuGet и настраивать поддержку.</span><span class="sxs-lookup"><span data-stu-id="d7404-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="d7404-173">Существуют отдельные модули форматирования для ввода и вывода.</span><span class="sxs-lookup"><span data-stu-id="d7404-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="d7404-174">Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d7404-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d7404-175">Форматировщики вывода используются для форматирования ответов.</span><span class="sxs-lookup"><span data-stu-id="d7404-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="d7404-176">Сведения о создании пользовательского форматировщика см. в статье [Пользовательские модули форматирования для веб-API в ASP.NET Core](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="d7404-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="d7404-177">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="d7404-177">Add XML format support</span></span>

<span data-ttu-id="d7404-178">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="d7404-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="d7404-179">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="d7404-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="d7404-180">При использовании приведенного выше кода методы контроллера возвращают соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="d7404-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="d7404-181">Настройка форматировщиков на основе System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="d7404-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="d7404-182">Функции для форматировщиков на основе `System.Text.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="d7404-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="d7404-183">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="d7404-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="d7404-184">Пример:</span><span class="sxs-lookup"><span data-stu-id="d7404-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="d7404-185">Добавление поддержки формата JSON на основе Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="d7404-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="d7404-186">До выпуска версии ASP.NET Core 3.0 по умолчанию использовались форматировщики JSON, реализованные с помощью пакета `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="d7404-187">В ASP.NET Core 3.0 или более поздней версии форматировщики JSON по умолчанию основаны на `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="d7404-188">Поддержка `Newtonsoft.Json` модулей форматирования и функций на основе данных доступна путем установки [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакета NuGet и его настройки в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d7404-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="d7404-189">В приведенном выше коде вызов `AddNewtonsoftJson` настраивает следующие функции веб-API, MVC и Razor страниц для использования `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="d7404-189">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="d7404-190">Модули форматирования ввода и вывода, считывающие и записывающие JSON</span><span class="sxs-lookup"><span data-stu-id="d7404-190">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="d7404-191">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="d7404-191">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="d7404-192">TempData</span><span class="sxs-lookup"><span data-stu-id="d7404-192">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="d7404-193">Возможно, некоторые функции не будут оптимально работать с форматировщиками на основе `System.Text.Json` и будут требовать ссылки на форматировщики на основе `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="d7404-194">Продолжайте использовать форматировщики на основе `Newtonsoft.Json`, если приложение:</span><span class="sxs-lookup"><span data-stu-id="d7404-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="d7404-195">Использует атрибут `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="d7404-196">Например, `[JsonProperty]` или `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="d7404-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="d7404-197">Настраивает параметры сериализации.</span><span class="sxs-lookup"><span data-stu-id="d7404-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="d7404-198">Зависит от функций, предоставляемых `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="d7404-199">Настраивается `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="d7404-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="d7404-200">В ASP.NET Core более ранних версий, чем версия 3.0, `JsonResult.SerializerSettings` принимает экземпляр `JsonSerializerSettings`, который относится к `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="d7404-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="d7404-201">Создается документация [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="d7404-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="d7404-202">Функции для форматировщиков на основе `Newtonsoft.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="d7404-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="d7404-203">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="d7404-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="d7404-204">Пример:</span><span class="sxs-lookup"><span data-stu-id="d7404-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="d7404-205">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="d7404-205">Add XML format support</span></span>

<span data-ttu-id="d7404-206">Для форматирования XML требуется пакет NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="d7404-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="d7404-207">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="d7404-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="d7404-208">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="d7404-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="d7404-209">При использовании приведенного выше кода методы контроллера должны возвращать соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="d7404-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="d7404-210">Указание формата</span><span class="sxs-lookup"><span data-stu-id="d7404-210">Specify a format</span></span>

<span data-ttu-id="d7404-211">Чтобы ограничить форматы ответа, примените [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) фильтр.</span><span class="sxs-lookup"><span data-stu-id="d7404-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="d7404-212">Как и большинство [фильтров](xref:mvc/controllers/filters), `[Produces]` можно применить к действию, контроллеру или глобальной области:</span><span class="sxs-lookup"><span data-stu-id="d7404-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="d7404-213">Предыдущий [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Фильтр:</span><span class="sxs-lookup"><span data-stu-id="d7404-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="d7404-214">Заставляет все действия в контроллере возвращать ответы в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="d7404-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="d7404-215">Если другие форматировщики настроены и клиент указывает другой формат, возвращается JSON.</span><span class="sxs-lookup"><span data-stu-id="d7404-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="d7404-216">Дополнительные сведения см. в статье [Фильтры в ASP.NET Core](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="d7404-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="d7404-217">Специальные форматировщики</span><span class="sxs-lookup"><span data-stu-id="d7404-217">Special case formatters</span></span>

<span data-ttu-id="d7404-218">Встроенные модули форматирования реализуют некоторые специальные возможности.</span><span class="sxs-lookup"><span data-stu-id="d7404-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="d7404-219">По умолчанию типы возвращаемых значений `string` форматируются как *text/plain* (*text/html*, если того требует заголовок `Accept`).</span><span class="sxs-lookup"><span data-stu-id="d7404-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="d7404-220">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="d7404-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="d7404-221">Форматировщики удаляются в методе `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d7404-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="d7404-222">Действия, у которых типом возвращаемого объекта является модель, возвращают ответ `204 No Content` при возврате значения `null`.</span><span class="sxs-lookup"><span data-stu-id="d7404-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="d7404-223">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="d7404-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="d7404-224">Приведенный ниже код удаляет `StringOutputFormatter` и `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="d7404-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="d7404-225">Без `StringOutputFormatter`, встроенный модуль форматирования JSON форматирует типы возвращаемого значения `string`.</span><span class="sxs-lookup"><span data-stu-id="d7404-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="d7404-226">Если встроенный модуль форматирования JSON удален и доступен модуль форматирования XML, то типы возвращаемого значения `string` форматирует модуль форматирования XML.</span><span class="sxs-lookup"><span data-stu-id="d7404-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="d7404-227">В противном случае, `string` типы возвращаемого значения возвращают `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="d7404-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="d7404-228">Без `HttpNoContentOutputFormatter` объекты со значением null форматируются с помощью настроенного модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="d7404-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="d7404-229">Пример:</span><span class="sxs-lookup"><span data-stu-id="d7404-229">For example:</span></span>

* <span data-ttu-id="d7404-230">Форматировщик JSON возвращает ответ с текстом `null`.</span><span class="sxs-lookup"><span data-stu-id="d7404-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="d7404-231">Форматировщик XML возвращает пустой XML-элемент с атрибутом `xsi:nil="true"`.</span><span class="sxs-lookup"><span data-stu-id="d7404-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="d7404-232">Сопоставления URL-адреса для формата ответа</span><span class="sxs-lookup"><span data-stu-id="d7404-232">Response format URL mappings</span></span>

<span data-ttu-id="d7404-233">Клиенты могут запрашивать определенный формат как часть URL-адреса, например:</span><span class="sxs-lookup"><span data-stu-id="d7404-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="d7404-234">В строке запроса или в части пути.</span><span class="sxs-lookup"><span data-stu-id="d7404-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="d7404-235">С использованием расширения файла конкретного формата, такого как XML или JSON.</span><span class="sxs-lookup"><span data-stu-id="d7404-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="d7404-236">Сопоставление из пути запроса должно быть указано в маршруте, используемом API.</span><span class="sxs-lookup"><span data-stu-id="d7404-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="d7404-237">Пример:</span><span class="sxs-lookup"><span data-stu-id="d7404-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="d7404-238">Этот маршрут позволяет задать запрошенный формат в качестве дополнительного расширения файла.</span><span class="sxs-lookup"><span data-stu-id="d7404-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="d7404-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Атрибут проверяет наличие значения формата в `RouteData` и сопоставляет формат ответа с соответствующим модулем форматирования при создании ответа.</span><span class="sxs-lookup"><span data-stu-id="d7404-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="d7404-240">Маршрут</span><span class="sxs-lookup"><span data-stu-id="d7404-240">Route</span></span>        |             <span data-ttu-id="d7404-241">Formatter</span><span class="sxs-lookup"><span data-stu-id="d7404-241">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="d7404-242">Модуль форматирования вывода по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d7404-242">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="d7404-243">Модуль форматирования JSON (если настроен)</span><span class="sxs-lookup"><span data-stu-id="d7404-243">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="d7404-244">Модуль форматирования XML (если настроен)</span><span class="sxs-lookup"><span data-stu-id="d7404-244">The XML formatter (if configured)</span></span>  |
