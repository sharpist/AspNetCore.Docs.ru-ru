---
<span data-ttu-id="56527-101">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-102">'Blazor'</span></span>
- <span data-ttu-id="56527-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-103">'Identity'</span></span>
- <span data-ttu-id="56527-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-105">'Razor'</span></span>
- <span data-ttu-id="56527-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="56527-107">Форматирование данных отклика в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56527-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="56527-108">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="56527-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="56527-109">Приложение MVC ASP.NET Core поддерживает форматирование данных ответа.</span><span class="sxs-lookup"><span data-stu-id="56527-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="56527-110">Данные ответа могут возвращаться в определенных форматах или в формате, запрошенном клиентом.</span><span class="sxs-lookup"><span data-stu-id="56527-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="56527-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56527-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="56527-112">Результаты действий для конкретного формата</span><span class="sxs-lookup"><span data-stu-id="56527-112">Format-specific Action Results</span></span>

<span data-ttu-id="56527-113">Некоторые типы результатов действий характерны для определенного формата, например <xref:Microsoft.AspNetCore.Mvc.JsonResult> и <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="56527-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="56527-114">Действия могут возвращать результаты в определенном формате независимо от настроек клиента.</span><span class="sxs-lookup"><span data-stu-id="56527-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="56527-115">Например, при возврате `JsonResult` возвращаются данные в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="56527-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="56527-116">При возврате `ContentResult` или строки возвращаются строковые данные в формате обычного текста.</span><span class="sxs-lookup"><span data-stu-id="56527-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="56527-117">Действие не должно возвращать данные конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="56527-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="56527-118">ASP.NET Core поддерживает любое возвращаемое значение объекта.</span><span class="sxs-lookup"><span data-stu-id="56527-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="56527-119">Результаты из действий, возвращающих объекты, которые не являются типами <xref:Microsoft.AspNetCore.Mvc.IActionResult>, сериализуются с помощью соответствующей реализации <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="56527-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="56527-120">Для получения дополнительной информации см. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="56527-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="56527-121">Встроенный вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> возвращает данные в формате JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="56527-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="56527-122">Скачанный пример возвращает список авторов.</span><span class="sxs-lookup"><span data-stu-id="56527-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="56527-123">При использовании средств разработчика в браузере (F12) или [Postman](https://www.getpostman.com/tools) с предыдущим кодом:</span><span class="sxs-lookup"><span data-stu-id="56527-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="56527-124">Отобразится заголовок ответа, содержащий **content-type:** `application/json; charset=utf-8`.</span><span class="sxs-lookup"><span data-stu-id="56527-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="56527-125">Отобразятся заголовки запросов.</span><span class="sxs-lookup"><span data-stu-id="56527-125">The request headers are displayed.</span></span> <span data-ttu-id="56527-126">Например, заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="56527-126">For example, the `Accept` header.</span></span> <span data-ttu-id="56527-127">Приведенный выше код игнорирует заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="56527-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="56527-128">Чтобы возвратить данные в формате обычного текста, используйте <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> и вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="56527-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="56527-129">В приведенном выше коде возвращаемым `Content-Type` является `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="56527-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="56527-130">При возврате строки возвращается `Content-Type` со значением `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="56527-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="56527-131">Для действий с несколькими возвращаемыми типами возвращается значение `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="56527-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="56527-132">Например, возвращаются различные коды состояния HTTP на основе результатов выполненных операций.</span><span class="sxs-lookup"><span data-stu-id="56527-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="56527-133">Согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="56527-133">Content negotiation</span></span>

<span data-ttu-id="56527-134">Согласование содержимого происходит, когда клиент задает [заголовок Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="56527-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="56527-135">Для ASP.NET Core по умолчанию используется формат [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="56527-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="56527-136">Согласование содержимого:</span><span class="sxs-lookup"><span data-stu-id="56527-136">Content negotiation is:</span></span>

* <span data-ttu-id="56527-137">реализуется с помощью <xref:Microsoft.AspNetCore.Mvc.ObjectResult>;</span><span class="sxs-lookup"><span data-stu-id="56527-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="56527-138">встроено в результаты действия с определенным кодом состояния, возвращаемые из вспомогательных методов;</span><span class="sxs-lookup"><span data-stu-id="56527-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="56527-139">вспомогательные методы результатов действия основаны на `ObjectResult`;</span><span class="sxs-lookup"><span data-stu-id="56527-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="56527-140">при возврате типа модели возвращаемым типом является `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="56527-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="56527-141">Следующий метод действия использует вспомогательные методы `Ok` и `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="56527-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="56527-142">По умолчанию ASP.NET Core поддерживает типы мультимедиа `application/json`, `text/json` и `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="56527-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="56527-143">Средства, такие как [Fiddler](https://www.telerik.com/fiddler) или [Postman](https://www.getpostman.com/tools), могут установить заголовок запроса `Accept`, чтобы указать формат возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="56527-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="56527-144">Если заголовок `Accept` содержит тип, который поддерживается сервером, возвращается этот тип.</span><span class="sxs-lookup"><span data-stu-id="56527-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="56527-145">Сведения о добавлении дополнительных форматировщиков приведены в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="56527-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="56527-146">Действия контроллера могут возвращать POCO (простые объекты CLR).</span><span class="sxs-lookup"><span data-stu-id="56527-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="56527-147">При возвращении POCO среда выполнения автоматически создает `ObjectResult`, который генерирует оболочку объекта.</span><span class="sxs-lookup"><span data-stu-id="56527-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="56527-148">Клиент получает отформатированный сериализованный объект.</span><span class="sxs-lookup"><span data-stu-id="56527-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="56527-149">Если возвращаемый объект имеет значение `null`, возвращается ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="56527-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="56527-150">Возвращение типа объекта:</span><span class="sxs-lookup"><span data-stu-id="56527-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="56527-151">В приведенном выше коде запрос допустимого псевдонима автора получает ответ `200 OK` с данными об авторе.</span><span class="sxs-lookup"><span data-stu-id="56527-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="56527-152">Запрос недопустимого псевдонима возвращает ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="56527-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="56527-153">Заголовок Accept</span><span class="sxs-lookup"><span data-stu-id="56527-153">The Accept header</span></span>

<span data-ttu-id="56527-154">*Согласование* содержимого выполняется только при наличии в запросе заголовка `Accept`.</span><span class="sxs-lookup"><span data-stu-id="56527-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="56527-155">Если запрос содержит заголовок Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="56527-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="56527-156">перечисляет типы мультимедиа в заголовке Accept в порядке предпочтения;</span><span class="sxs-lookup"><span data-stu-id="56527-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="56527-157">пытается найти форматировщик, который может создать ответ в одном из указанных форматов.</span><span class="sxs-lookup"><span data-stu-id="56527-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="56527-158">Если форматировщик, который может удовлетворить запрос клиента, не найден, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="56527-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="56527-159">возвращает значение `406 Not Acceptable`, если <xref:Microsoft.AspNetCore.Mvc.MvcOptions> задано, или:</span><span class="sxs-lookup"><span data-stu-id="56527-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="56527-160">пытается найти первый форматировщик, который может создать ответ.</span><span class="sxs-lookup"><span data-stu-id="56527-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="56527-161">Если форматировщик, обеспечивающий требуемый формат, не настроен, используется первый форматировщик, способный отформатировать данный объект.</span><span class="sxs-lookup"><span data-stu-id="56527-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="56527-162">Если в запросе не отображается заголовок `Accept`:</span><span class="sxs-lookup"><span data-stu-id="56527-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="56527-163">Для сериализации ответа используется первый форматировщик, который может работать с объектом.</span><span class="sxs-lookup"><span data-stu-id="56527-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="56527-164">Согласование не выполняется.</span><span class="sxs-lookup"><span data-stu-id="56527-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="56527-165">Сервер определяет возвращаемый формат.</span><span class="sxs-lookup"><span data-stu-id="56527-165">The server is determining what format to return.</span></span>

<span data-ttu-id="56527-166">Если заголовок Accept содержит `*/*`, он игнорируется, если только `RespectBrowserAcceptHeader` не имеет значение true в <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="56527-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="56527-167">Браузеры и согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="56527-167">Browsers and content negotiation</span></span>

<span data-ttu-id="56527-168">В отличие от обычных клиентов API, веб-браузеры предоставляют заголовки `Accept`.</span><span class="sxs-lookup"><span data-stu-id="56527-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="56527-169">Веб-браузер определяет множество форматов, включая подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="56527-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="56527-170">Если платформа обнаруживает, что запрос поступает из браузера, по умолчанию выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="56527-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="56527-171">Заголовок `Accept` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="56527-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="56527-172">Содержимое возвращается в формате JSON, если не указано иначе.</span><span class="sxs-lookup"><span data-stu-id="56527-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="56527-173">Это обеспечивает более согласованное взаимодействие между браузерами при использовании API.</span><span class="sxs-lookup"><span data-stu-id="56527-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="56527-174">Чтобы настроить приложение для учета заголовков Accept в браузере, установите для параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> значение `true`:</span><span class="sxs-lookup"><span data-stu-id="56527-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="56527-175">Настройка форматировщиков</span><span class="sxs-lookup"><span data-stu-id="56527-175">Configure formatters</span></span>

<span data-ttu-id="56527-176">В приложениях, которым требуется поддержка дополнительных форматов, можно добавлять соответствующие пакеты NuGet и настраивать поддержку.</span><span class="sxs-lookup"><span data-stu-id="56527-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="56527-177">Существуют отдельные модули форматирования для ввода и вывода.</span><span class="sxs-lookup"><span data-stu-id="56527-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="56527-178">Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="56527-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="56527-179">Форматировщики вывода используются для форматирования ответов.</span><span class="sxs-lookup"><span data-stu-id="56527-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="56527-180">Сведения о создании пользовательского форматировщика см. в статье [Пользовательские модули форматирования для веб-API в ASP.NET Core](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="56527-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="56527-181">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="56527-181">Add XML format support</span></span>

<span data-ttu-id="56527-182">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="56527-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="56527-183">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="56527-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="56527-184">При использовании приведенного выше кода методы контроллера возвращают соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="56527-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="56527-185">Настройка форматировщиков на основе System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="56527-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="56527-186">Функции для форматировщиков на основе `System.Text.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="56527-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="56527-187">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="56527-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="56527-188">Пример:</span><span class="sxs-lookup"><span data-stu-id="56527-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="56527-189">Добавление поддержки формата JSON на основе Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="56527-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="56527-190">До выпуска версии ASP.NET Core 3.0 по умолчанию использовались форматировщики JSON, реализованные с помощью пакета `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="56527-191">В ASP.NET Core 3.0 или более поздней версии форматировщики JSON по умолчанию основаны на `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="56527-192">Поддержка `Newtonsoft.Json` модулей форматирования и функций на основе данных доступна путем установки [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакета NuGet и его настройки в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="56527-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="56527-193">Возможно, некоторые функции не будут оптимально работать с форматировщиками на основе `System.Text.Json` и будут требовать ссылки на форматировщики на основе `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="56527-194">Продолжайте использовать форматировщики на основе `Newtonsoft.Json`, если приложение:</span><span class="sxs-lookup"><span data-stu-id="56527-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="56527-195">Использует атрибут `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="56527-196">Например, `[JsonProperty]` или `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="56527-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="56527-197">Настраивает параметры сериализации.</span><span class="sxs-lookup"><span data-stu-id="56527-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="56527-198">Зависит от функций, предоставляемых `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="56527-199">Настраивается `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="56527-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="56527-200">В ASP.NET Core более ранних версий, чем версия 3.0, `JsonResult.SerializerSettings` принимает экземпляр `JsonSerializerSettings`, который относится к `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="56527-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="56527-201">Создается документация [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="56527-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="56527-202">Функции для форматировщиков на основе `Newtonsoft.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="56527-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="56527-203">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="56527-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="56527-204">Пример:</span><span class="sxs-lookup"><span data-stu-id="56527-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="56527-205">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="56527-205">Add XML format support</span></span>

<span data-ttu-id="56527-206">Для форматирования XML требуется пакет NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="56527-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="56527-207">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="56527-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="56527-208">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="56527-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="56527-209">При использовании приведенного выше кода методы контроллера должны возвращать соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="56527-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="56527-210">Указание формата</span><span class="sxs-lookup"><span data-stu-id="56527-210">Specify a format</span></span>

<span data-ttu-id="56527-211">Чтобы ограничить форматы ответа, примените [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) фильтр.</span><span class="sxs-lookup"><span data-stu-id="56527-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="56527-212">Как и большинство [фильтров](xref:mvc/controllers/filters), `[Produces]` можно применить к действию, контроллеру или глобальной области:</span><span class="sxs-lookup"><span data-stu-id="56527-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="56527-213">Предыдущий [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Фильтр:</span><span class="sxs-lookup"><span data-stu-id="56527-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="56527-214">Заставляет все действия в контроллере возвращать ответы в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="56527-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="56527-215">Если другие форматировщики настроены и клиент указывает другой формат, возвращается JSON.</span><span class="sxs-lookup"><span data-stu-id="56527-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="56527-216">Дополнительные сведения см. в статье [Фильтры в ASP.NET Core](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="56527-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="56527-217">Специальные форматировщики</span><span class="sxs-lookup"><span data-stu-id="56527-217">Special case formatters</span></span>

<span data-ttu-id="56527-218">Встроенные модули форматирования реализуют некоторые специальные возможности.</span><span class="sxs-lookup"><span data-stu-id="56527-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="56527-219">По умолчанию типы возвращаемых значений `string` форматируются как *text/plain* (*text/html*, если того требует заголовок `Accept`).</span><span class="sxs-lookup"><span data-stu-id="56527-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="56527-220">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="56527-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="56527-221">Форматировщики удаляются в методе `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56527-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="56527-222">Действия, у которых типом возвращаемого объекта является модель, возвращают ответ `204 No Content` при возврате значения `null`.</span><span class="sxs-lookup"><span data-stu-id="56527-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="56527-223">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="56527-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="56527-224">Приведенный ниже код удаляет `StringOutputFormatter` и `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="56527-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="56527-225">Без `StringOutputFormatter`, встроенный модуль форматирования JSON форматирует типы возвращаемого значения `string`.</span><span class="sxs-lookup"><span data-stu-id="56527-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="56527-226">Если встроенный модуль форматирования JSON удален и доступен модуль форматирования XML, то типы возвращаемого значения `string` форматирует модуль форматирования XML.</span><span class="sxs-lookup"><span data-stu-id="56527-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="56527-227">В противном случае, `string` типы возвращаемого значения возвращают `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="56527-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="56527-228">Без `HttpNoContentOutputFormatter` объекты со значением null форматируются с помощью настроенного модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="56527-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="56527-229">Пример:</span><span class="sxs-lookup"><span data-stu-id="56527-229">For example:</span></span>

* <span data-ttu-id="56527-230">Форматировщик JSON возвращает ответ с текстом `null`.</span><span class="sxs-lookup"><span data-stu-id="56527-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="56527-231">Форматировщик XML возвращает пустой XML-элемент с атрибутом `xsi:nil="true"`.</span><span class="sxs-lookup"><span data-stu-id="56527-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="56527-232">Сопоставления URL-адреса для формата ответа</span><span class="sxs-lookup"><span data-stu-id="56527-232">Response format URL mappings</span></span>

<span data-ttu-id="56527-233">Клиенты могут запрашивать определенный формат как часть URL-адреса, например:</span><span class="sxs-lookup"><span data-stu-id="56527-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="56527-234">В строке запроса или в части пути.</span><span class="sxs-lookup"><span data-stu-id="56527-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="56527-235">С использованием расширения файла конкретного формата, такого как XML или JSON.</span><span class="sxs-lookup"><span data-stu-id="56527-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="56527-236">Сопоставление из пути запроса должно быть указано в маршруте, используемом API.</span><span class="sxs-lookup"><span data-stu-id="56527-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="56527-237">Пример:</span><span class="sxs-lookup"><span data-stu-id="56527-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="56527-238">Этот маршрут позволяет задать запрошенный формат в качестве дополнительного расширения файла.</span><span class="sxs-lookup"><span data-stu-id="56527-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="56527-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Атрибут проверяет наличие значения формата в `RouteData` и сопоставляет формат ответа с соответствующим модулем форматирования при создании ответа.</span><span class="sxs-lookup"><span data-stu-id="56527-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="56527-240">Маршрут</span><span class="sxs-lookup"><span data-stu-id="56527-240">Route</span></span>        |             <span data-ttu-id="56527-241">Formatter</span><span class="sxs-lookup"><span data-stu-id="56527-241">Formatter</span></span>              |
|---
<span data-ttu-id="56527-242">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-243">'Blazor'</span></span>
- <span data-ttu-id="56527-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-244">'Identity'</span></span>
- <span data-ttu-id="56527-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-246">'Razor'</span></span>
- <span data-ttu-id="56527-247">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-248">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-249">'Blazor'</span></span>
- <span data-ttu-id="56527-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-250">'Identity'</span></span>
- <span data-ttu-id="56527-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-252">'Razor'</span></span>
- <span data-ttu-id="56527-253">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-254">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-255">'Blazor'</span></span>
- <span data-ttu-id="56527-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-256">'Identity'</span></span>
- <span data-ttu-id="56527-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-258">'Razor'</span></span>
- <span data-ttu-id="56527-259">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-260">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-261">'Blazor'</span></span>
- <span data-ttu-id="56527-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-262">'Identity'</span></span>
- <span data-ttu-id="56527-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-264">'Razor'</span></span>
- <span data-ttu-id="56527-265">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-266">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-267">'Blazor'</span></span>
- <span data-ttu-id="56527-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-268">'Identity'</span></span>
- <span data-ttu-id="56527-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-270">'Razor'</span></span>
- <span data-ttu-id="56527-271">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-272">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-273">'Blazor'</span></span>
- <span data-ttu-id="56527-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-274">'Identity'</span></span>
- <span data-ttu-id="56527-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-276">'Razor'</span></span>
- <span data-ttu-id="56527-277">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-278">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-279">'Blazor'</span></span>
- <span data-ttu-id="56527-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-280">'Identity'</span></span>
- <span data-ttu-id="56527-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-282">'Razor'</span></span>
- <span data-ttu-id="56527-283">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-284">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-285">'Blazor'</span></span>
- <span data-ttu-id="56527-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-286">'Identity'</span></span>
- <span data-ttu-id="56527-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-288">'Razor'</span></span>
- <span data-ttu-id="56527-289">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-290">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-291">'Blazor'</span></span>
- <span data-ttu-id="56527-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-292">'Identity'</span></span>
- <span data-ttu-id="56527-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-294">'Razor'</span></span>
- <span data-ttu-id="56527-295">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-296">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-297">'Blazor'</span></span>
- <span data-ttu-id="56527-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-298">'Identity'</span></span>
- <span data-ttu-id="56527-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-300">'Razor'</span></span>
- <span data-ttu-id="56527-301">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-301">'SignalR' uid:</span></span> 

<span data-ttu-id="56527-302">------------| Заголовок---: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-303">'Blazor'</span></span>
- <span data-ttu-id="56527-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-304">'Identity'</span></span>
- <span data-ttu-id="56527-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-306">'Razor'</span></span>
- <span data-ttu-id="56527-307">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-308">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-309">'Blazor'</span></span>
- <span data-ttu-id="56527-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-310">'Identity'</span></span>
- <span data-ttu-id="56527-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-312">'Razor'</span></span>
- <span data-ttu-id="56527-313">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-314">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-315">'Blazor'</span></span>
- <span data-ttu-id="56527-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-316">'Identity'</span></span>
- <span data-ttu-id="56527-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-318">'Razor'</span></span>
- <span data-ttu-id="56527-319">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-320">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-321">'Blazor'</span></span>
- <span data-ttu-id="56527-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-322">'Identity'</span></span>
- <span data-ttu-id="56527-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-324">'Razor'</span></span>
- <span data-ttu-id="56527-325">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-326">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-327">'Blazor'</span></span>
- <span data-ttu-id="56527-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-328">'Identity'</span></span>
- <span data-ttu-id="56527-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-330">'Razor'</span></span>
- <span data-ttu-id="56527-331">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-332">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-333">'Blazor'</span></span>
- <span data-ttu-id="56527-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-334">'Identity'</span></span>
- <span data-ttu-id="56527-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-336">'Razor'</span></span>
- <span data-ttu-id="56527-337">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-338">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-339">'Blazor'</span></span>
- <span data-ttu-id="56527-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-340">'Identity'</span></span>
- <span data-ttu-id="56527-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-342">'Razor'</span></span>
- <span data-ttu-id="56527-343">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-344">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-345">'Blazor'</span></span>
- <span data-ttu-id="56527-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-346">'Identity'</span></span>
- <span data-ttu-id="56527-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-348">'Razor'</span></span>
- <span data-ttu-id="56527-349">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-350">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-351">'Blazor'</span></span>
- <span data-ttu-id="56527-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-352">'Identity'</span></span>
- <span data-ttu-id="56527-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-354">'Razor'</span></span>
- <span data-ttu-id="56527-355">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-356">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-357">'Blazor'</span></span>
- <span data-ttu-id="56527-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-358">'Identity'</span></span>
- <span data-ttu-id="56527-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-360">'Razor'</span></span>
- <span data-ttu-id="56527-361">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-362">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-363">'Blazor'</span></span>
- <span data-ttu-id="56527-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-364">'Identity'</span></span>
- <span data-ttu-id="56527-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-366">'Razor'</span></span>
- <span data-ttu-id="56527-367">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-368">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-369">'Blazor'</span></span>
- <span data-ttu-id="56527-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-370">'Identity'</span></span>
- <span data-ttu-id="56527-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-372">'Razor'</span></span>
- <span data-ttu-id="56527-373">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-374">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-375">'Blazor'</span></span>
- <span data-ttu-id="56527-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-376">'Identity'</span></span>
- <span data-ttu-id="56527-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-378">'Razor'</span></span>
- <span data-ttu-id="56527-379">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-380">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-381">'Blazor'</span></span>
- <span data-ttu-id="56527-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-382">'Identity'</span></span>
- <span data-ttu-id="56527-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-384">'Razor'</span></span>
- <span data-ttu-id="56527-385">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-386">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-387">'Blazor'</span></span>
- <span data-ttu-id="56527-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-388">'Identity'</span></span>
- <span data-ttu-id="56527-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-390">'Razor'</span></span>
- <span data-ttu-id="56527-391">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56527-392">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="56527-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56527-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56527-393">'Blazor'</span></span>
- <span data-ttu-id="56527-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56527-394">'Identity'</span></span>
- <span data-ttu-id="56527-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56527-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="56527-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56527-396">'Razor'</span></span>
- <span data-ttu-id="56527-397">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="56527-397">'SignalR' uid:</span></span> 

<span data-ttu-id="56527-398">------------------| |   `/api/products/5`    |    Модуль форматирования выходных данных по умолчанию | | `/api/products/5.json` | Модуль форматирования JSON (если настроен) | | `/api/products/5.xml`  | Модуль форматирования XML (если настроен) |</span><span class="sxs-lookup"><span data-stu-id="56527-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
