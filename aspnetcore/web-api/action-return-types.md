---
<span data-ttu-id="96f10-101">Title: типы возвращаемых действий контроллера в ASP.NET Core автор веб-API: скоттаддие Description: сведения об использовании различных типов возвращаемых методов действия контроллера в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="96f10-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="96f10-102">MS. author: скаддие MS. Custom: MVC MS. Дата: 02/03/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f10-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="96f10-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f10-103">'Blazor'</span></span>
- <span data-ttu-id="96f10-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f10-104">'Identity'</span></span>
- <span data-ttu-id="96f10-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f10-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f10-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f10-106">'Razor'</span></span>
- <span data-ttu-id="96f10-107">' SignalR ' UID: Web-API/Action-Returns-Types</span><span class="sxs-lookup"><span data-stu-id="96f10-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="96f10-108">Типы возвращаемых значений действий контроллера в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96f10-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="96f10-109">Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="96f10-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="96f10-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96f10-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="96f10-111">ASP.NET Core предоставляет следующие параметры для типов возвращаемых значений действий контроллера веб-API:</span><span class="sxs-lookup"><span data-stu-id="96f10-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="96f10-112">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="96f10-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="96f10-113">IActionResult</span><span class="sxs-lookup"><span data-stu-id="96f10-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="96f10-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="96f10-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="96f10-115">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="96f10-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="96f10-116">IActionResult</span><span class="sxs-lookup"><span data-stu-id="96f10-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="96f10-117">В этом документе объясняется, когда лучше использовать каждый тип возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="96f10-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="96f10-118">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="96f10-118">Specific type</span></span>

<span data-ttu-id="96f10-119">Простейшее действие возвращает элементарный или сложный тип данных (например, `string` или пользовательский тип объекта).</span><span class="sxs-lookup"><span data-stu-id="96f10-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="96f10-120">Рассмотрим следующее действие, которое возвращает коллекцию пользовательских объектов `Product`:</span><span class="sxs-lookup"><span data-stu-id="96f10-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="96f10-121">Если не известны условия, которые необходимо соблюдать при выполнении действия, конкретного типа будет достаточно.</span><span class="sxs-lookup"><span data-stu-id="96f10-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="96f10-122">Предыдущее действие не принимает параметры, поэтому проверка ограничений параметров не требуется.</span><span class="sxs-lookup"><span data-stu-id="96f10-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="96f10-123">Если возможны множественные возвращаемые типы, то распространенным типом возвращаемого <xref:Microsoft.AspNetCore.Mvc.ActionResult> значения является примитивный или сложный тип.</span><span class="sxs-lookup"><span data-stu-id="96f10-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="96f10-124">Для выполнения этого типа действий необходимы [IActionResult](#iactionresult-type) или [ActionResult \<T> ](#actionresultt-type) .</span><span class="sxs-lookup"><span data-stu-id="96f10-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="96f10-125">В этом документе представлено несколько примеров с несколькими типами возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="96f10-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="96f10-126">Возврат IEnumerable \<T> или иасинценумерабле\<T></span><span class="sxs-lookup"><span data-stu-id="96f10-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="96f10-127">В ASP.NET Core 2.2 и более ранних версиях получение интерфейса <xref:System.Collections.Generic.IEnumerable%601> из действия приводит к тому, что сериализатор выполняет синхронную итерацию операции сбора.</span><span class="sxs-lookup"><span data-stu-id="96f10-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="96f10-128">В результате вызовы блокируются, что может стать причиной перегрузки пула потоков.</span><span class="sxs-lookup"><span data-stu-id="96f10-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="96f10-129">Представьте, что Entity Framework (EF) Core используется веб-API для доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="96f10-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="96f10-130">Во время сериализации выполняется синхронное перечисление для типа возвращаемого значения следующего действия:</span><span class="sxs-lookup"><span data-stu-id="96f10-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="96f10-131">Чтобы не допустить синхронного перечисления и блокировки операций ожидания для базы данных в ASP.NET Core 2.2 и более ранних версий, вызовите `ToListAsync`:</span><span class="sxs-lookup"><span data-stu-id="96f10-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="96f10-132">В ASP.NET Core 3.0 и более поздних версиях получение `IAsyncEnumerable<T>` из действия:</span><span class="sxs-lookup"><span data-stu-id="96f10-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="96f10-133">больше не приводит к синхронной итерации;</span><span class="sxs-lookup"><span data-stu-id="96f10-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="96f10-134">по эффективности не отличается от получения <xref:System.Collections.Generic.IEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="96f10-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="96f10-135">ASP.NET Core 3.0 и более поздних версий помещает результаты следующего действия в буфер перед предоставлением его сериализатору:</span><span class="sxs-lookup"><span data-stu-id="96f10-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="96f10-136">Мы рекомендуем объявлять тип возвращаемого значения для сигнатуры действия как `IAsyncEnumerable<T>` для гарантированного выполнения асинхронной итерации.</span><span class="sxs-lookup"><span data-stu-id="96f10-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="96f10-137">То есть режим итерации зависит от возвращаемого базового конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="96f10-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="96f10-138">MVC автоматически буферизует все конкретные типы, которые реализуют `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="96f10-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="96f10-139">Рассмотрим следующее действие, которое возвращает записи о продуктах со сниженной ценой как `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="96f10-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="96f10-140">Эквивалентом `IAsyncEnumerable<Product>` для предшествующего действия является:</span><span class="sxs-lookup"><span data-stu-id="96f10-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="96f10-141">Начиная с версии ASP.NET Core 3.0, оба предшествующих действия не являются блокирующими.</span><span class="sxs-lookup"><span data-stu-id="96f10-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="96f10-142">Тип IActionResult</span><span class="sxs-lookup"><span data-stu-id="96f10-142">IActionResult type</span></span>

<span data-ttu-id="96f10-143">Тип возвращаемого значения <xref:Microsoft.AspNetCore.Mvc.IActionResult> можно использовать, если в действии допускаются несколько типов возвращаемого значения `ActionResult`.</span><span class="sxs-lookup"><span data-stu-id="96f10-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="96f10-144">Типы `ActionResult` представляют различные коды состояния HTTP.</span><span class="sxs-lookup"><span data-stu-id="96f10-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="96f10-145">Любой неабстрактный класс, унаследованный от квалификаторов `ActionResult` в виде допустимого типа возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="96f10-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="96f10-146">Некоторые распространенные типы возвращаемых значений в этой категории: <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) и <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="96f10-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="96f10-147">Кроме того, удобные методы в классе <xref:Microsoft.AspNetCore.Mvc.ControllerBase> можно использовать для получения типов `ActionResult` из действия.</span><span class="sxs-lookup"><span data-stu-id="96f10-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="96f10-148">Например, `return BadRequest();` — это сокращенная форма `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="96f10-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="96f10-149">Так как в этом типе действий существует несколько возвращаемых типов и путей, необходимо свободно использовать [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="96f10-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="96f10-150">Этот атрибут создает описательные сведения об ответе для страниц справки по веб-API, создаваемых с помощью таких инструментов, как [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="96f10-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="96f10-151">`[ProducesResponseType]` указывает известные типы и коды состояния HTTP, возвращаемые действием.</span><span class="sxs-lookup"><span data-stu-id="96f10-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="96f10-152">Синхронное действие</span><span class="sxs-lookup"><span data-stu-id="96f10-152">Synchronous action</span></span>

<span data-ttu-id="96f10-153">Рассмотрим следующее синхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="96f10-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="96f10-154">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="96f10-154">In the preceding action:</span></span>

* <span data-ttu-id="96f10-155">возвращается код состояния 404, если продукт, представленный `id`, не существует в базовом хранилище данных;</span><span class="sxs-lookup"><span data-stu-id="96f10-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="96f10-156">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> вызывается как сокращение для `return new NotFoundResult();`.</span><span class="sxs-lookup"><span data-stu-id="96f10-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="96f10-157">Код состояния 200 возвращается с объектом `Product`, если продукт не существует.</span><span class="sxs-lookup"><span data-stu-id="96f10-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="96f10-158">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> вызывается как сокращение для `return new OkObjectResult(product);`.</span><span class="sxs-lookup"><span data-stu-id="96f10-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="96f10-159">Асинхронное действие</span><span class="sxs-lookup"><span data-stu-id="96f10-159">Asynchronous action</span></span>

<span data-ttu-id="96f10-160">Рассмотрим следующее асинхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="96f10-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="96f10-161">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="96f10-161">In the preceding action:</span></span>

* <span data-ttu-id="96f10-162">Код состояния 400 возвращается, если описание продукта содержит строку XYZ Widget.</span><span class="sxs-lookup"><span data-stu-id="96f10-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="96f10-163">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> вызывается как сокращение для `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="96f10-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="96f10-164">Код состояния 201 генерируется удобным методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта.</span><span class="sxs-lookup"><span data-stu-id="96f10-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="96f10-165">В качестве альтернативы вызову `CreatedAtAction` можно использовать `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="96f10-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="96f10-166">В этом пути к коду объект `Product` предоставляется в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="96f10-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="96f10-167">Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.</span><span class="sxs-lookup"><span data-stu-id="96f10-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="96f10-168">Например, следующая модель указывает на то, что запросы должны включать свойства `Name` и `Description`.</span><span class="sxs-lookup"><span data-stu-id="96f10-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="96f10-169">Если `Name` и `Description` не были указаны в запросе, происходит сбой проверки модели.</span><span class="sxs-lookup"><span data-stu-id="96f10-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="96f10-170">Если [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) применяется атрибут в ASP.NET Core 2,1 или более поздней версии, ошибки проверки модели приводят к коду состояния 400.</span><span class="sxs-lookup"><span data-stu-id="96f10-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="96f10-171">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="96f10-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="96f10-172">\<T>Тип ActionResult</span><span class="sxs-lookup"><span data-stu-id="96f10-172">ActionResult\<T> type</span></span>

<span data-ttu-id="96f10-173">В ASP.NET Core 2,1 введен тип возвращаемого значения [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) для действий контроллера веб-API.</span><span class="sxs-lookup"><span data-stu-id="96f10-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="96f10-174">Он позволяет возвращать тип, производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> или [определенный тип](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="96f10-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="96f10-175">`ActionResult<T>` имеет следующие преимущества по сравнению с [типом IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="96f10-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="96f10-176">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Свойство атрибута можно исключить.</span><span class="sxs-lookup"><span data-stu-id="96f10-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="96f10-177">Например, `[ProducesResponseType(200, Type = typeof(Product))]` упрощается до `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="96f10-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="96f10-178">Ожидаемый тип возвращаемого значения действия вместо этого выводится из `T` в `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="96f10-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="96f10-179">[Операторы неявного приведения](/dotnet/csharp/language-reference/keywords/implicit) поддерживают преобразование `T` и `ActionResult` в `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="96f10-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="96f10-180">`T` преобразуется в <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, то есть `return new ObjectResult(T);` упрощается до `return T;`.</span><span class="sxs-lookup"><span data-stu-id="96f10-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="96f10-181">C# не поддерживает операторы неявных приведений в интерфейсах.</span><span class="sxs-lookup"><span data-stu-id="96f10-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="96f10-182">Следовательно, для преобразования в конкретный тип необходимо использовать `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="96f10-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="96f10-183">Например, использование `IEnumerable` не работает в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="96f10-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="96f10-184">Один из способов исправить приведенный выше код — возвратить `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="96f10-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="96f10-185">Большинство действий имеют тип возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="96f10-185">Most actions have a specific return type.</span></span> <span data-ttu-id="96f10-186">При выполнении действия могут возникнуть непредвиденные условия, и в этом случае определенный тип не возвращается.</span><span class="sxs-lookup"><span data-stu-id="96f10-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="96f10-187">Например, входной параметр действия может не пройти проверку модели.</span><span class="sxs-lookup"><span data-stu-id="96f10-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="96f10-188">В этом случае обычно возвращается подходящий тип `ActionResult` вместо конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="96f10-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="96f10-189">Синхронное действие</span><span class="sxs-lookup"><span data-stu-id="96f10-189">Synchronous action</span></span>

<span data-ttu-id="96f10-190">Рассмотрим синхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="96f10-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="96f10-191">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="96f10-191">In the preceding action:</span></span>

* <span data-ttu-id="96f10-192">возвращается код состояния 404, если продукт не существует в базе данных;</span><span class="sxs-lookup"><span data-stu-id="96f10-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="96f10-193">возвращается код состояния 200 с соответствующим объектом `Product`, если продукт существует.</span><span class="sxs-lookup"><span data-stu-id="96f10-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="96f10-194">До версии ASP.NET Core 2.1 строка `return product;` имела бы вид `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="96f10-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="96f10-195">Асинхронное действие</span><span class="sxs-lookup"><span data-stu-id="96f10-195">Asynchronous action</span></span>

<span data-ttu-id="96f10-196">Рассмотрим асинхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="96f10-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="96f10-197">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="96f10-197">In the preceding action:</span></span>

* <span data-ttu-id="96f10-198">Код состояния 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) возвращается средой выполнения ASP.NET Core, если:</span><span class="sxs-lookup"><span data-stu-id="96f10-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="96f10-199">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут применен, и проверка модели завершается неудачно.</span><span class="sxs-lookup"><span data-stu-id="96f10-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="96f10-200">Описание продукта содержит XYZ Widget.</span><span class="sxs-lookup"><span data-stu-id="96f10-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="96f10-201">Код состояния 201 генерируется методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта.</span><span class="sxs-lookup"><span data-stu-id="96f10-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="96f10-202">В этом пути к коду объект `Product` предоставляется в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="96f10-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="96f10-203">Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.</span><span class="sxs-lookup"><span data-stu-id="96f10-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="96f10-204">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="96f10-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
