---
title: Привязка модели в ASP.NET Core
author: rick-anderson
description: Узнайте, как работает привязка модели в ASP.NET Core и как настроить ее поведение.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393890"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="d25cf-103">Привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d25cf-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d25cf-104">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="d25cf-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="d25cf-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d25cf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="d25cf-106">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-106">What is Model binding</span></span>

<span data-ttu-id="d25cf-107">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="d25cf-108">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="d25cf-109">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="d25cf-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="d25cf-110">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="d25cf-110">Model binding automates this process.</span></span> <span data-ttu-id="d25cf-111">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="d25cf-111">The model binding system:</span></span>

* <span data-ttu-id="d25cf-112">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="d25cf-113">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="d25cf-114">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="d25cf-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="d25cf-115">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="d25cf-116">Пример</span><span class="sxs-lookup"><span data-stu-id="d25cf-116">Example</span></span>

<span data-ttu-id="d25cf-117">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="d25cf-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="d25cf-118">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="d25cf-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="d25cf-119">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="d25cf-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="d25cf-120">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="d25cf-121">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="d25cf-122">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="d25cf-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="d25cf-123">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="d25cf-124">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="d25cf-125">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="d25cf-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="d25cf-126">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="d25cf-127">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="d25cf-128">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="d25cf-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="d25cf-129">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="d25cf-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="d25cf-130">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="d25cf-131">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="d25cf-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="d25cf-132">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="d25cf-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="d25cf-133">Цели</span><span class="sxs-lookup"><span data-stu-id="d25cf-133">Targets</span></span>

<span data-ttu-id="d25cf-134">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="d25cf-135">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="d25cf-136">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="d25cf-137">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="d25cf-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="d25cf-138">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="d25cf-138">[BindProperty] attribute</span></span>

<span data-ttu-id="d25cf-139">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="d25cf-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="d25cf-140">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="d25cf-140">[BindProperties] attribute</span></span>

<span data-ttu-id="d25cf-141">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d25cf-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="d25cf-142">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="d25cf-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="d25cf-143">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="d25cf-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="d25cf-144">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="d25cf-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="d25cf-145">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="d25cf-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="d25cf-146">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="d25cf-147">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="d25cf-148">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="d25cf-149">Источники</span><span class="sxs-lookup"><span data-stu-id="d25cf-149">Sources</span></span>

<span data-ttu-id="d25cf-150">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="d25cf-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="d25cf-151">Поля формы</span><span class="sxs-lookup"><span data-stu-id="d25cf-151">Form fields</span></span>
1. <span data-ttu-id="d25cf-152">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="d25cf-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="d25cf-153">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="d25cf-153">Route data</span></span>
1. <span data-ttu-id="d25cf-154">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="d25cf-154">Query string parameters</span></span>
1. <span data-ttu-id="d25cf-155">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="d25cf-155">Uploaded files</span></span>

<span data-ttu-id="d25cf-156">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="d25cf-157">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="d25cf-157">There are a few exceptions:</span></span>

* <span data-ttu-id="d25cf-158">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="d25cf-159">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="d25cf-160">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="d25cf-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="d25cf-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="d25cf-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="d25cf-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="d25cf-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="d25cf-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="d25cf-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="d25cf-166">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="d25cf-166">These attributes:</span></span>

* <span data-ttu-id="d25cf-167">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="d25cf-168">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="d25cf-169">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="d25cf-170">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="d25cf-171">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="d25cf-171">[FromBody] attribute</span></span>

<span data-ttu-id="d25cf-172">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="d25cf-173">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="d25cf-174">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="d25cf-175">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="d25cf-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="d25cf-176">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="d25cf-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="d25cf-177">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="d25cf-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="d25cf-178">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-178">In the preceding example:</span></span>

* <span data-ttu-id="d25cf-179">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="d25cf-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="d25cf-180">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="d25cf-181">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="d25cf-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="d25cf-182">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="d25cf-183">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="d25cf-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="d25cf-184">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="d25cf-185">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="d25cf-185">Additional sources</span></span>

<span data-ttu-id="d25cf-186">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="d25cf-187">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="d25cf-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="d25cf-188">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="d25cf-189">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="d25cf-189">To get data from a new source:</span></span>

* <span data-ttu-id="d25cf-190">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="d25cf-191">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="d25cf-192">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="d25cf-193">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="d25cf-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="d25cf-194">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="d25cf-195">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="d25cf-196">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="d25cf-197">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-197">No source for a model property</span></span>

<span data-ttu-id="d25cf-198">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="d25cf-199">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="d25cf-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="d25cf-200">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="d25cf-201">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="d25cf-202">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="d25cf-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="d25cf-203">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="d25cf-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="d25cf-204">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="d25cf-205">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="d25cf-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="d25cf-206">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="d25cf-207">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="d25cf-208">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="d25cf-208">Type conversion errors</span></span>

<span data-ttu-id="d25cf-209">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="d25cf-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="d25cf-210">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="d25cf-211">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="d25cf-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="d25cf-212">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="d25cf-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="d25cf-213">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="d25cf-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="d25cf-214">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="d25cf-215">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="d25cf-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="d25cf-216">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="d25cf-217">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="d25cf-218">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d25cf-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="d25cf-219">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="d25cf-220">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="d25cf-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="d25cf-221">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="d25cf-222">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="d25cf-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="d25cf-223">Простые типы</span><span class="sxs-lookup"><span data-stu-id="d25cf-223">Simple types</span></span>

<span data-ttu-id="d25cf-224">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="d25cf-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="d25cf-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="d25cf-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="d25cf-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="d25cf-227">Char</span><span class="sxs-lookup"><span data-stu-id="d25cf-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="d25cf-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="d25cf-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="d25cf-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="d25cf-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="d25cf-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="d25cf-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="d25cf-231">Double</span><span class="sxs-lookup"><span data-stu-id="d25cf-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="d25cf-232">Перечисление</span><span class="sxs-lookup"><span data-stu-id="d25cf-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="d25cf-233">Устройства</span><span class="sxs-lookup"><span data-stu-id="d25cf-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="d25cf-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="d25cf-235">Single</span><span class="sxs-lookup"><span data-stu-id="d25cf-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="d25cf-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="d25cf-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="d25cf-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="d25cf-238">URI</span><span class="sxs-lookup"><span data-stu-id="d25cf-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="d25cf-239">Version</span><span class="sxs-lookup"><span data-stu-id="d25cf-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="d25cf-240">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="d25cf-240">Complex types</span></span>

<span data-ttu-id="d25cf-241">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="d25cf-242">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d25cf-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="d25cf-243">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="d25cf-244">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="d25cf-245">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="d25cf-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="d25cf-246">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="d25cf-247">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="d25cf-248">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="d25cf-249">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="d25cf-249">Prefix = parameter name</span></span>

<span data-ttu-id="d25cf-250">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="d25cf-251">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="d25cf-252">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="d25cf-253">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="d25cf-253">Prefix = property name</span></span>

<span data-ttu-id="d25cf-254">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="d25cf-255">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d25cf-256">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="d25cf-257">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="d25cf-257">Custom prefix</span></span>

<span data-ttu-id="d25cf-258">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="d25cf-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="d25cf-259">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d25cf-260">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="d25cf-261">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="d25cf-261">Attributes for complex type targets</span></span>

<span data-ttu-id="d25cf-262">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="d25cf-263">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="d25cf-264">Они ***не*** влияют на модули форматирования ввода, которые обрабатывают опубликованные тексты JSON и XML-запросов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="d25cf-265">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="d25cf-266">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="d25cf-266">[Bind] attribute</span></span>

<span data-ttu-id="d25cf-267">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="d25cf-268">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="d25cf-269">`[Bind]` не ***влияет на*** модули форматирования ввода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="d25cf-270">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="d25cf-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="d25cf-271">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="d25cf-272">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="d25cf-273">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="d25cf-274">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="d25cf-275">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="d25cf-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="d25cf-276">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="d25cf-276">[BindRequired] attribute</span></span>

<span data-ttu-id="d25cf-277">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d25cf-278">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="d25cf-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="d25cf-279">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="d25cf-280">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="d25cf-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="d25cf-281">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="d25cf-281">[BindNever] attribute</span></span>

<span data-ttu-id="d25cf-282">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d25cf-283">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="d25cf-284">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="d25cf-285">Коллекции</span><span class="sxs-lookup"><span data-stu-id="d25cf-285">Collections</span></span>

<span data-ttu-id="d25cf-286">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d25cf-287">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d25cf-288">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-288">For example:</span></span>

* <span data-ttu-id="d25cf-289">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="d25cf-290">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="d25cf-291">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="d25cf-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="d25cf-292">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d25cf-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="d25cf-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="d25cf-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="d25cf-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="d25cf-295">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="d25cf-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="d25cf-296">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="d25cf-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="d25cf-297">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="d25cf-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="d25cf-298">Словари</span><span class="sxs-lookup"><span data-stu-id="d25cf-298">Dictionaries</span></span>

<span data-ttu-id="d25cf-299">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d25cf-300">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d25cf-301">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-301">For example:</span></span>

* <span data-ttu-id="d25cf-302">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="d25cf-303">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="d25cf-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="d25cf-304">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d25cf-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="d25cf-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="d25cf-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="d25cf-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="d25cf-307">Привязка конструктора и типы записей</span><span class="sxs-lookup"><span data-stu-id="d25cf-307">Constructor binding and record types</span></span>

<span data-ttu-id="d25cf-308">Привязка модели требует, чтобы сложные типы имели конструктор без параметров.</span><span class="sxs-lookup"><span data-stu-id="d25cf-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="d25cf-309">`System.Text.Json` `Newtonsoft.Json` Модули форматирования ввода и поддерживают десериализацию классов, у которых нет конструктора без параметров.</span><span class="sxs-lookup"><span data-stu-id="d25cf-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="d25cf-310">В C# 9 появились типы записей, которые представляют собой отличный способ краткого представления данных по сети.</span><span class="sxs-lookup"><span data-stu-id="d25cf-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="d25cf-311">ASP.NET Core добавляет поддержку привязки модели и проверку типов записей с помощью одного конструктора:</span><span class="sxs-lookup"><span data-stu-id="d25cf-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="d25cf-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="d25cf-313">При проверке типов записей среда выполнения ищет метаданные проверки специально для параметров, а не для свойств.</span><span class="sxs-lookup"><span data-stu-id="d25cf-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="d25cf-314">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="d25cf-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="d25cf-315">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d25cf-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="d25cf-316">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="d25cf-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="d25cf-317">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="d25cf-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="d25cf-318">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="d25cf-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="d25cf-319">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="d25cf-320">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d25cf-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="d25cf-321">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="d25cf-322">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="d25cf-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="d25cf-323">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="d25cf-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="d25cf-324">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="d25cf-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="d25cf-325">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="d25cf-325">Special data types</span></span>

<span data-ttu-id="d25cf-326">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="d25cf-327">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="d25cf-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="d25cf-328">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="d25cf-329">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="d25cf-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="d25cf-330">CancellationToken</span></span>

<span data-ttu-id="d25cf-331">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="d25cf-332">FormCollection</span><span class="sxs-lookup"><span data-stu-id="d25cf-332">FormCollection</span></span>

<span data-ttu-id="d25cf-333">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="d25cf-334">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="d25cf-334">Input formatters</span></span>

<span data-ttu-id="d25cf-335">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="d25cf-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="d25cf-336">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="d25cf-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="d25cf-337">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="d25cf-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="d25cf-338">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="d25cf-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="d25cf-339">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="d25cf-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="d25cf-340">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="d25cf-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="d25cf-341">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="d25cf-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="d25cf-342">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="d25cf-343">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="d25cf-344">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="d25cf-345">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="d25cf-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="d25cf-346">Настройка привязки модели с помощью форматировщиков входных данных</span><span class="sxs-lookup"><span data-stu-id="d25cf-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="d25cf-347">Форматировщик входных данных полностью отвечает за чтение данных из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="d25cf-348">Чтобы настроить этот процесс, настройте API-интерфейсы, используемые форматировщиками входных данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="d25cf-349">В этом разделе описывается, как настроить форматировщик входных данных на основе `System.Text.Json` так, чтобы он понимал настраиваемый тип с именем `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="d25cf-350">Рассмотрим следующую модель, которая содержит настраиваемое свойство `ObjectId` с именем `Id`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="d25cf-351">Чтобы настроить процесс привязки модели при использовании `System.Text.Json`, создайте производный класс на основе класса <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="d25cf-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="d25cf-352">Чтобы использовать пользовательский преобразователь, примените к типу атрибут <xref:System.Text.Json.Serialization.JsonConverterAttribute>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="d25cf-353">В следующем примере тип `ObjectId` настраивается с `ObjectIdConverter` в качестве пользовательского преобразователя:</span><span class="sxs-lookup"><span data-stu-id="d25cf-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="d25cf-354">Дополнительные сведения см. в статье [How to write custom converters for JSON serialization (marshalling) in .NET](/dotnet/standard/serialization/system-text-json-converters-how-to) (Создание пользовательских преобразователей для сериализации JSON (маршалинг) в .NET).</span><span class="sxs-lookup"><span data-stu-id="d25cf-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="d25cf-355">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="d25cf-356">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="d25cf-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="d25cf-357">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="d25cf-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="d25cf-358">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="d25cf-359">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d25cf-360">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="d25cf-361">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d25cf-362">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="d25cf-363">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-363">Custom model binders</span></span>

<span data-ttu-id="d25cf-364">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="d25cf-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="d25cf-365">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="d25cf-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="d25cf-366">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="d25cf-366">Manual model binding</span></span> 

<span data-ttu-id="d25cf-367">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="d25cf-368">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="d25cf-369">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="d25cf-370">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="d25cf-371">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="d25cf-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> использует поставщиков значений для получения данных из текста формы, строки запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="d25cf-373">`TryUpdateModelAsync` как правило:</span><span class="sxs-lookup"><span data-stu-id="d25cf-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="d25cf-374">Используется с Razor страницами и приложениями MVC с использованием контроллеров и представлений для предотвращения перебора.</span><span class="sxs-lookup"><span data-stu-id="d25cf-374">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="d25cf-375">не используется с веб-API, если только не используется из данных формы, строк запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="d25cf-376">Конечные точки веб-API, которые используют JSON, применяют [форматировщики входных данных](#input-formatters) для десериализации текста запроса в объект.</span><span class="sxs-lookup"><span data-stu-id="d25cf-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="d25cf-377">Дополнительные сведения см. в разделе [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="d25cf-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="d25cf-378">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="d25cf-378">[FromServices] attribute</span></span>

<span data-ttu-id="d25cf-379">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="d25cf-380">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="d25cf-381">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d25cf-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d25cf-382">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="d25cf-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d25cf-383">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d25cf-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d25cf-384">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="d25cf-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="d25cf-385">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d25cf-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="d25cf-386">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-386">What is Model binding</span></span>

<span data-ttu-id="d25cf-387">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-387">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="d25cf-388">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="d25cf-389">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="d25cf-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="d25cf-390">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="d25cf-390">Model binding automates this process.</span></span> <span data-ttu-id="d25cf-391">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="d25cf-391">The model binding system:</span></span>

* <span data-ttu-id="d25cf-392">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="d25cf-393">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-393">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="d25cf-394">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="d25cf-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="d25cf-395">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="d25cf-396">Пример</span><span class="sxs-lookup"><span data-stu-id="d25cf-396">Example</span></span>

<span data-ttu-id="d25cf-397">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="d25cf-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="d25cf-398">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="d25cf-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="d25cf-399">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="d25cf-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="d25cf-400">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="d25cf-401">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="d25cf-402">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="d25cf-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="d25cf-403">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="d25cf-404">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="d25cf-405">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="d25cf-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="d25cf-406">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="d25cf-407">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="d25cf-408">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="d25cf-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="d25cf-409">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="d25cf-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="d25cf-410">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="d25cf-411">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="d25cf-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="d25cf-412">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="d25cf-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="d25cf-413">Цели</span><span class="sxs-lookup"><span data-stu-id="d25cf-413">Targets</span></span>

<span data-ttu-id="d25cf-414">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="d25cf-415">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="d25cf-416">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-416">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="d25cf-417">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="d25cf-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="d25cf-418">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="d25cf-418">[BindProperty] attribute</span></span>

<span data-ttu-id="d25cf-419">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="d25cf-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="d25cf-420">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="d25cf-420">[BindProperties] attribute</span></span>

<span data-ttu-id="d25cf-421">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d25cf-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="d25cf-422">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="d25cf-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="d25cf-423">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="d25cf-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="d25cf-424">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="d25cf-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="d25cf-425">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="d25cf-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="d25cf-426">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="d25cf-427">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="d25cf-428">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="d25cf-429">Источники</span><span class="sxs-lookup"><span data-stu-id="d25cf-429">Sources</span></span>

<span data-ttu-id="d25cf-430">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="d25cf-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="d25cf-431">Поля формы</span><span class="sxs-lookup"><span data-stu-id="d25cf-431">Form fields</span></span>
1. <span data-ttu-id="d25cf-432">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="d25cf-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="d25cf-433">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="d25cf-433">Route data</span></span>
1. <span data-ttu-id="d25cf-434">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="d25cf-434">Query string parameters</span></span>
1. <span data-ttu-id="d25cf-435">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="d25cf-435">Uploaded files</span></span>

<span data-ttu-id="d25cf-436">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="d25cf-437">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="d25cf-437">There are a few exceptions:</span></span>

* <span data-ttu-id="d25cf-438">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="d25cf-439">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="d25cf-440">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="d25cf-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="d25cf-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="d25cf-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="d25cf-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="d25cf-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="d25cf-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="d25cf-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="d25cf-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="d25cf-446">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="d25cf-446">These attributes:</span></span>

* <span data-ttu-id="d25cf-447">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="d25cf-448">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="d25cf-449">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="d25cf-450">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="d25cf-451">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="d25cf-451">[FromBody] attribute</span></span>

<span data-ttu-id="d25cf-452">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="d25cf-453">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="d25cf-454">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="d25cf-455">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="d25cf-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="d25cf-456">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="d25cf-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="d25cf-457">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="d25cf-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="d25cf-458">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="d25cf-458">In the preceding example:</span></span>

* <span data-ttu-id="d25cf-459">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="d25cf-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="d25cf-460">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="d25cf-461">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="d25cf-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="d25cf-462">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="d25cf-463">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="d25cf-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="d25cf-464">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="d25cf-465">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="d25cf-465">Additional sources</span></span>

<span data-ttu-id="d25cf-466">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-466">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="d25cf-467">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="d25cf-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="d25cf-468">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-468">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="d25cf-469">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="d25cf-469">To get data from a new source:</span></span>

* <span data-ttu-id="d25cf-470">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="d25cf-471">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="d25cf-472">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="d25cf-473">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="d25cf-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="d25cf-474">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="d25cf-475">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="d25cf-476">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-476">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="d25cf-477">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-477">No source for a model property</span></span>

<span data-ttu-id="d25cf-478">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="d25cf-479">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="d25cf-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="d25cf-480">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="d25cf-481">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="d25cf-482">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="d25cf-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="d25cf-483">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="d25cf-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="d25cf-484">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="d25cf-485">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="d25cf-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="d25cf-486">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="d25cf-487">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="d25cf-488">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="d25cf-488">Type conversion errors</span></span>

<span data-ttu-id="d25cf-489">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="d25cf-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="d25cf-490">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="d25cf-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="d25cf-491">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="d25cf-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="d25cf-492">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="d25cf-492">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="d25cf-493">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="d25cf-493">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="d25cf-494">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="d25cf-495">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="d25cf-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="d25cf-496">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="d25cf-497">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="d25cf-498">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d25cf-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="d25cf-499">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="d25cf-500">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="d25cf-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="d25cf-501">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="d25cf-502">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="d25cf-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="d25cf-503">Простые типы</span><span class="sxs-lookup"><span data-stu-id="d25cf-503">Simple types</span></span>

<span data-ttu-id="d25cf-504">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="d25cf-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="d25cf-505">Boolean</span><span class="sxs-lookup"><span data-stu-id="d25cf-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="d25cf-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="d25cf-507">Char</span><span class="sxs-lookup"><span data-stu-id="d25cf-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="d25cf-508">DateTime</span><span class="sxs-lookup"><span data-stu-id="d25cf-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="d25cf-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="d25cf-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="d25cf-510">Decimal</span><span class="sxs-lookup"><span data-stu-id="d25cf-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="d25cf-511">Double</span><span class="sxs-lookup"><span data-stu-id="d25cf-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="d25cf-512">Перечисление</span><span class="sxs-lookup"><span data-stu-id="d25cf-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="d25cf-513">Устройства</span><span class="sxs-lookup"><span data-stu-id="d25cf-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="d25cf-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="d25cf-515">Single</span><span class="sxs-lookup"><span data-stu-id="d25cf-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="d25cf-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="d25cf-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="d25cf-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="d25cf-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="d25cf-518">URI</span><span class="sxs-lookup"><span data-stu-id="d25cf-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="d25cf-519">Version</span><span class="sxs-lookup"><span data-stu-id="d25cf-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="d25cf-520">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="d25cf-520">Complex types</span></span>

<span data-ttu-id="d25cf-521">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="d25cf-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="d25cf-522">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d25cf-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="d25cf-523">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="d25cf-524">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="d25cf-525">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="d25cf-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="d25cf-526">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="d25cf-527">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="d25cf-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="d25cf-528">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="d25cf-529">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="d25cf-529">Prefix = parameter name</span></span>

<span data-ttu-id="d25cf-530">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="d25cf-531">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="d25cf-532">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="d25cf-533">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="d25cf-533">Prefix = property name</span></span>

<span data-ttu-id="d25cf-534">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="d25cf-535">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d25cf-536">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="d25cf-537">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="d25cf-537">Custom prefix</span></span>

<span data-ttu-id="d25cf-538">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="d25cf-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="d25cf-539">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d25cf-540">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="d25cf-541">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="d25cf-541">Attributes for complex type targets</span></span>

<span data-ttu-id="d25cf-542">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="d25cf-543">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="d25cf-544">Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="d25cf-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="d25cf-545">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="d25cf-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="d25cf-546">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="d25cf-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="d25cf-547">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="d25cf-547">[BindRequired] attribute</span></span>

<span data-ttu-id="d25cf-548">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d25cf-549">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="d25cf-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="d25cf-550">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="d25cf-551">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="d25cf-551">[BindNever] attribute</span></span>

<span data-ttu-id="d25cf-552">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d25cf-553">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="d25cf-554">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="d25cf-555">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="d25cf-555">[Bind] attribute</span></span>

<span data-ttu-id="d25cf-556">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="d25cf-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="d25cf-557">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="d25cf-558">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="d25cf-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="d25cf-559">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="d25cf-560">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="d25cf-561">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="d25cf-562">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="d25cf-563">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="d25cf-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="d25cf-564">Коллекции</span><span class="sxs-lookup"><span data-stu-id="d25cf-564">Collections</span></span>

<span data-ttu-id="d25cf-565">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d25cf-566">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d25cf-567">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-567">For example:</span></span>

* <span data-ttu-id="d25cf-568">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="d25cf-569">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="d25cf-569">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="d25cf-570">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="d25cf-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="d25cf-571">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d25cf-572">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="d25cf-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="d25cf-573">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="d25cf-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="d25cf-574">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="d25cf-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="d25cf-575">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="d25cf-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="d25cf-576">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="d25cf-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="d25cf-577">Словари</span><span class="sxs-lookup"><span data-stu-id="d25cf-577">Dictionaries</span></span>

<span data-ttu-id="d25cf-578">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="d25cf-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d25cf-579">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d25cf-580">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-580">For example:</span></span>

* <span data-ttu-id="d25cf-581">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="d25cf-582">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="d25cf-582">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="d25cf-583">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d25cf-584">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="d25cf-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="d25cf-585">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="d25cf-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="d25cf-586">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="d25cf-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="d25cf-587">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d25cf-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="d25cf-588">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="d25cf-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="d25cf-589">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="d25cf-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="d25cf-590">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="d25cf-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="d25cf-591">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="d25cf-592">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d25cf-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="d25cf-593">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="d25cf-594">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="d25cf-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="d25cf-595">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="d25cf-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="d25cf-596">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="d25cf-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="d25cf-597">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="d25cf-597">Special data types</span></span>

<span data-ttu-id="d25cf-598">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="d25cf-599">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="d25cf-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="d25cf-600">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="d25cf-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="d25cf-601">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="d25cf-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="d25cf-602">CancellationToken</span></span>

<span data-ttu-id="d25cf-603">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="d25cf-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="d25cf-604">FormCollection</span><span class="sxs-lookup"><span data-stu-id="d25cf-604">FormCollection</span></span>

<span data-ttu-id="d25cf-605">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="d25cf-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="d25cf-606">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="d25cf-606">Input formatters</span></span>

<span data-ttu-id="d25cf-607">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="d25cf-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="d25cf-608">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="d25cf-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="d25cf-609">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="d25cf-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="d25cf-610">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="d25cf-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="d25cf-611">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="d25cf-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="d25cf-612">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="d25cf-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="d25cf-613">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="d25cf-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="d25cf-614">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="d25cf-615">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="d25cf-616">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="d25cf-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="d25cf-617">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="d25cf-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="d25cf-618">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="d25cf-619">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="d25cf-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="d25cf-620">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="d25cf-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="d25cf-621">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="d25cf-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="d25cf-622">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d25cf-623">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="d25cf-624">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d25cf-625">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="d25cf-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="d25cf-626">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="d25cf-626">Custom model binders</span></span>

<span data-ttu-id="d25cf-627">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="d25cf-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="d25cf-628">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="d25cf-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="d25cf-629">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="d25cf-629">Manual model binding</span></span>

<span data-ttu-id="d25cf-630">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d25cf-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="d25cf-631">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="d25cf-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="d25cf-632">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="d25cf-633">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="d25cf-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="d25cf-634">Пример:</span><span class="sxs-lookup"><span data-stu-id="d25cf-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="d25cf-635">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="d25cf-635">[FromServices] attribute</span></span>

<span data-ttu-id="d25cf-636">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="d25cf-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="d25cf-637">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="d25cf-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="d25cf-638">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d25cf-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d25cf-639">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="d25cf-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d25cf-640">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d25cf-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
