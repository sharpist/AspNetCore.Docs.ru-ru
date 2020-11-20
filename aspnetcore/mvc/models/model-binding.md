---
title: Привязка модели в ASP.NET Core
author: rick-anderson
description: Узнайте, как работает привязка модели в ASP.NET Core и как настроить ее поведение.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- appsettings.json
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
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981938"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="6a487-103">Привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a487-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a487-104">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="6a487-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="6a487-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a487-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="6a487-106">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="6a487-106">What is Model binding</span></span>

<span data-ttu-id="6a487-107">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6a487-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="6a487-108">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="6a487-109">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="6a487-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="6a487-110">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="6a487-110">Model binding automates this process.</span></span> <span data-ttu-id="6a487-111">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="6a487-111">The model binding system:</span></span>

* <span data-ttu-id="6a487-112">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="6a487-113">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="6a487-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="6a487-114">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="6a487-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="6a487-115">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="6a487-116">Пример</span><span class="sxs-lookup"><span data-stu-id="6a487-116">Example</span></span>

<span data-ttu-id="6a487-117">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="6a487-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="6a487-118">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="6a487-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="6a487-119">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="6a487-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="6a487-120">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="6a487-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="6a487-121">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="6a487-122">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="6a487-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="6a487-123">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="6a487-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="6a487-124">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="6a487-125">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="6a487-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="6a487-126">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="6a487-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="6a487-127">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="6a487-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="6a487-128">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="6a487-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="6a487-129">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="6a487-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="6a487-130">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="6a487-131">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="6a487-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="6a487-132">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="6a487-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="6a487-133">Цели</span><span class="sxs-lookup"><span data-stu-id="6a487-133">Targets</span></span>

<span data-ttu-id="6a487-134">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="6a487-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="6a487-135">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="6a487-136">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="6a487-137">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="6a487-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="6a487-138">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="6a487-138">[BindProperty] attribute</span></span>

<span data-ttu-id="6a487-139">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="6a487-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="6a487-140">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="6a487-140">[BindProperties] attribute</span></span>

<span data-ttu-id="6a487-141">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="6a487-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="6a487-142">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="6a487-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="6a487-143">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="6a487-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="6a487-144">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="6a487-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="6a487-145">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="6a487-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="6a487-146">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="6a487-147">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="6a487-148">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="6a487-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="6a487-149">Источники</span><span class="sxs-lookup"><span data-stu-id="6a487-149">Sources</span></span>

<span data-ttu-id="6a487-150">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="6a487-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="6a487-151">Поля формы</span><span class="sxs-lookup"><span data-stu-id="6a487-151">Form fields</span></span>
1. <span data-ttu-id="6a487-152">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="6a487-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="6a487-153">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="6a487-153">Route data</span></span>
1. <span data-ttu-id="6a487-154">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="6a487-154">Query string parameters</span></span>
1. <span data-ttu-id="6a487-155">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="6a487-155">Uploaded files</span></span>

<span data-ttu-id="6a487-156">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="6a487-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="6a487-157">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="6a487-157">There are a few exceptions:</span></span>

* <span data-ttu-id="6a487-158">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="6a487-159">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="6a487-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="6a487-160">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="6a487-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="6a487-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="6a487-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="6a487-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="6a487-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="6a487-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="6a487-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="6a487-166">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="6a487-166">These attributes:</span></span>

* <span data-ttu-id="6a487-167">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="6a487-168">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="6a487-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="6a487-169">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="6a487-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="6a487-170">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="6a487-171">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="6a487-171">[FromBody] attribute</span></span>

<span data-ttu-id="6a487-172">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="6a487-173">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="6a487-174">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="6a487-175">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="6a487-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="6a487-176">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="6a487-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="6a487-177">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="6a487-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="6a487-178">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-178">In the preceding example:</span></span>

* <span data-ttu-id="6a487-179">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="6a487-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="6a487-180">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="6a487-181">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="6a487-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="6a487-182">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="6a487-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="6a487-183">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="6a487-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="6a487-184">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6a487-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="6a487-185">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="6a487-185">Additional sources</span></span>

<span data-ttu-id="6a487-186">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="6a487-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="6a487-187">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="6a487-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="6a487-188">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="6a487-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="6a487-189">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="6a487-189">To get data from a new source:</span></span>

* <span data-ttu-id="6a487-190">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="6a487-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="6a487-191">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="6a487-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="6a487-192">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="6a487-193">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="6a487-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="6a487-194">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a487-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="6a487-195">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="6a487-196">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="6a487-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="6a487-197">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="6a487-197">No source for a model property</span></span>

<span data-ttu-id="6a487-198">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="6a487-199">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="6a487-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="6a487-200">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="6a487-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="6a487-201">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="6a487-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="6a487-202">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="6a487-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="6a487-203">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="6a487-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="6a487-204">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="6a487-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="6a487-205">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="6a487-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="6a487-206">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="6a487-207">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="6a487-208">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="6a487-208">Type conversion errors</span></span>

<span data-ttu-id="6a487-209">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="6a487-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="6a487-210">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="6a487-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="6a487-211">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="6a487-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="6a487-212">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="6a487-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="6a487-213">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="6a487-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="6a487-214">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="6a487-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="6a487-215">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="6a487-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="6a487-216">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="6a487-217">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="6a487-218">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6a487-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="6a487-219">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="6a487-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="6a487-220">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="6a487-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="6a487-221">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="6a487-222">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="6a487-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="6a487-223">Простые типы</span><span class="sxs-lookup"><span data-stu-id="6a487-223">Simple types</span></span>

<span data-ttu-id="6a487-224">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="6a487-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="6a487-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="6a487-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="6a487-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="6a487-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="6a487-227">Char</span><span class="sxs-lookup"><span data-stu-id="6a487-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="6a487-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="6a487-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="6a487-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="6a487-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="6a487-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="6a487-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="6a487-231">Double</span><span class="sxs-lookup"><span data-stu-id="6a487-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="6a487-232">Enum</span><span class="sxs-lookup"><span data-stu-id="6a487-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="6a487-233">Устройства</span><span class="sxs-lookup"><span data-stu-id="6a487-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="6a487-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="6a487-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="6a487-235">Single</span><span class="sxs-lookup"><span data-stu-id="6a487-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="6a487-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="6a487-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="6a487-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="6a487-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="6a487-238">URI</span><span class="sxs-lookup"><span data-stu-id="6a487-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="6a487-239">Version</span><span class="sxs-lookup"><span data-stu-id="6a487-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="6a487-240">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="6a487-240">Complex types</span></span>

<span data-ttu-id="6a487-241">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="6a487-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="6a487-242">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6a487-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="6a487-243">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="6a487-244">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="6a487-245">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="6a487-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="6a487-246">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="6a487-247">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="6a487-248">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="6a487-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="6a487-249">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="6a487-249">Prefix = parameter name</span></span>

<span data-ttu-id="6a487-250">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="6a487-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="6a487-251">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="6a487-252">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="6a487-253">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="6a487-253">Prefix = property name</span></span>

<span data-ttu-id="6a487-254">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6a487-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="6a487-255">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6a487-256">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="6a487-257">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="6a487-257">Custom prefix</span></span>

<span data-ttu-id="6a487-258">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="6a487-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="6a487-259">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6a487-260">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="6a487-261">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="6a487-261">Attributes for complex type targets</span></span>

<span data-ttu-id="6a487-262">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="6a487-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="6a487-263">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="6a487-264">Они делают \***Not** _ на модули форматирования ввода, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="6a487-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="6a487-265">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="6a487-266">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="6a487-266">[Bind] attribute</span></span>

<span data-ttu-id="6a487-267">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="6a487-268">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="6a487-269">`[Bind]` не _\*_влияет на_\*_ модули форматирования ввода.</span><span class="sxs-lookup"><span data-stu-id="6a487-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="6a487-270">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="6a487-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="6a487-271">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="6a487-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="6a487-272">`[Bind]`Атрибут можно использовать для защиты от перенесения в сценариях _create \*.</span><span class="sxs-lookup"><span data-stu-id="6a487-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="6a487-273">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="6a487-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="6a487-274">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="6a487-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="6a487-275">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="6a487-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="6a487-276">Атрибут [Моделбиндер]</span><span class="sxs-lookup"><span data-stu-id="6a487-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="6a487-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> может применяться к типам, свойствам или параметрам.</span><span class="sxs-lookup"><span data-stu-id="6a487-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="6a487-278">Он позволяет указать тип связывателя модели, используемый для привязки конкретного экземпляра или типа.</span><span class="sxs-lookup"><span data-stu-id="6a487-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="6a487-279">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="6a487-280">`[ModelBinder]`Атрибут также можно использовать для изменения имени свойства или параметра, если оно привязано к модели:</span><span class="sxs-lookup"><span data-stu-id="6a487-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="6a487-281">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="6a487-281">[BindRequired] attribute</span></span>

<span data-ttu-id="6a487-282">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6a487-283">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="6a487-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="6a487-284">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="6a487-285">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="6a487-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="6a487-286">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="6a487-286">[BindNever] attribute</span></span>

<span data-ttu-id="6a487-287">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6a487-288">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="6a487-289">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="6a487-290">Коллекции</span><span class="sxs-lookup"><span data-stu-id="6a487-290">Collections</span></span>

<span data-ttu-id="6a487-291">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6a487-292">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6a487-293">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-293">For example:</span></span>

* <span data-ttu-id="6a487-294">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="6a487-295">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="6a487-295">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="6a487-296">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="6a487-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="6a487-297">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6a487-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="6a487-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="6a487-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="6a487-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="6a487-300">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="6a487-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="6a487-301">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="6a487-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="6a487-302">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="6a487-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="6a487-303">Словари</span><span class="sxs-lookup"><span data-stu-id="6a487-303">Dictionaries</span></span>

<span data-ttu-id="6a487-304">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6a487-305">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6a487-306">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-306">For example:</span></span>

* <span data-ttu-id="6a487-307">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="6a487-308">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="6a487-308">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="6a487-309">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6a487-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="6a487-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="6a487-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="6a487-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="6a487-312">Привязка конструктора и типы записей</span><span class="sxs-lookup"><span data-stu-id="6a487-312">Constructor binding and record types</span></span>

<span data-ttu-id="6a487-313">Привязка модели требует, чтобы сложные типы имели конструктор без параметров.</span><span class="sxs-lookup"><span data-stu-id="6a487-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="6a487-314">`System.Text.Json` `Newtonsoft.Json` Модули форматирования ввода и поддерживают десериализацию классов, у которых нет конструктора без параметров.</span><span class="sxs-lookup"><span data-stu-id="6a487-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="6a487-315">В C# 9 появились типы записей, которые представляют собой отличный способ краткого представления данных по сети.</span><span class="sxs-lookup"><span data-stu-id="6a487-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="6a487-316">ASP.NET Core добавляет поддержку привязки модели и проверку типов записей с помощью одного конструктора:</span><span class="sxs-lookup"><span data-stu-id="6a487-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="6a487-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="6a487-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="6a487-318">При проверке типов записей среда выполнения ищет метаданные проверки специально для параметров, а не для свойств.</span><span class="sxs-lookup"><span data-stu-id="6a487-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="6a487-319">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="6a487-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="6a487-320">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6a487-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="6a487-321">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="6a487-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="6a487-322">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="6a487-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="6a487-323">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="6a487-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="6a487-324">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="6a487-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="6a487-325">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="6a487-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="6a487-326">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="6a487-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="6a487-327">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="6a487-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="6a487-328">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="6a487-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="6a487-329">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="6a487-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="6a487-330">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="6a487-330">Special data types</span></span>

<span data-ttu-id="6a487-331">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="6a487-332">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="6a487-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="6a487-333">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="6a487-334">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="6a487-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="6a487-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="6a487-335">CancellationToken</span></span>

<span data-ttu-id="6a487-336">При необходимости действия могут быть привязаны `CancellationToken` как параметр.</span><span class="sxs-lookup"><span data-stu-id="6a487-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="6a487-337">Это привязывает этот <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> сигнал при прерывании соединения, лежащего в основе HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="6a487-338">Действия могут использовать этот параметр для отмены долго выполняющихся асинхронных операций, выполняемых в рамках действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="6a487-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="6a487-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="6a487-339">FormCollection</span></span>

<span data-ttu-id="6a487-340">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="6a487-341">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="6a487-341">Input formatters</span></span>

<span data-ttu-id="6a487-342">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="6a487-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="6a487-343">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="6a487-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="6a487-344">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="6a487-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="6a487-345">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="6a487-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="6a487-346">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="6a487-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="6a487-347">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="6a487-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="6a487-348">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="6a487-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="6a487-349">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="6a487-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="6a487-350">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="6a487-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="6a487-351">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="6a487-352">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="6a487-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="6a487-353">Настройка привязки модели с помощью форматировщиков входных данных</span><span class="sxs-lookup"><span data-stu-id="6a487-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="6a487-354">Форматировщик входных данных полностью отвечает за чтение данных из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="6a487-355">Чтобы настроить этот процесс, настройте API-интерфейсы, используемые форматировщиками входных данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="6a487-356">В этом разделе описывается, как настроить форматировщик входных данных на основе `System.Text.Json` так, чтобы он понимал настраиваемый тип с именем `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="6a487-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="6a487-357">Рассмотрим следующую модель, которая содержит настраиваемое свойство `ObjectId` с именем `Id`:</span><span class="sxs-lookup"><span data-stu-id="6a487-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="6a487-358">Чтобы настроить процесс привязки модели при использовании `System.Text.Json`, создайте производный класс на основе класса <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="6a487-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="6a487-359">Чтобы использовать пользовательский преобразователь, примените к типу атрибут <xref:System.Text.Json.Serialization.JsonConverterAttribute>.</span><span class="sxs-lookup"><span data-stu-id="6a487-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="6a487-360">В следующем примере тип `ObjectId` настраивается с `ObjectIdConverter` в качестве пользовательского преобразователя:</span><span class="sxs-lookup"><span data-stu-id="6a487-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="6a487-361">Дополнительные сведения см. в статье [How to write custom converters for JSON serialization (marshalling) in .NET](/dotnet/standard/serialization/system-text-json-converters-how-to) (Создание пользовательских преобразователей для сериализации JSON (маршалинг) в .NET).</span><span class="sxs-lookup"><span data-stu-id="6a487-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="6a487-362">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="6a487-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="6a487-363">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="6a487-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="6a487-364">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="6a487-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="6a487-365">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="6a487-366">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a487-367">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="6a487-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="6a487-368">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a487-369">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="6a487-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="6a487-370">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="6a487-370">Custom model binders</span></span>

<span data-ttu-id="6a487-371">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="6a487-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="6a487-372">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="6a487-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="6a487-373">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="6a487-373">Manual model binding</span></span> 

<span data-ttu-id="6a487-374">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6a487-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="6a487-375">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="6a487-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="6a487-376">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="6a487-377">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="6a487-378">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="6a487-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> использует поставщиков значений для получения данных из текста формы, строки запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="6a487-380">`TryUpdateModelAsync` как правило:</span><span class="sxs-lookup"><span data-stu-id="6a487-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="6a487-381">Используется с Razor страницами и приложениями MVC с использованием контроллеров и представлений для предотвращения перебора.</span><span class="sxs-lookup"><span data-stu-id="6a487-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="6a487-382">не используется с веб-API, если только не используется из данных формы, строк запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="6a487-383">Конечные точки веб-API, которые используют JSON, применяют [форматировщики входных данных](#input-formatters) для десериализации текста запроса в объект.</span><span class="sxs-lookup"><span data-stu-id="6a487-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="6a487-384">Дополнительные сведения см. в разделе [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="6a487-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="6a487-385">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="6a487-385">[FromServices] attribute</span></span>

<span data-ttu-id="6a487-386">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="6a487-387">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="6a487-388">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a487-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6a487-389">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="6a487-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a487-390">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6a487-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6a487-391">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="6a487-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="6a487-392">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a487-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="6a487-393">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="6a487-393">What is Model binding</span></span>

<span data-ttu-id="6a487-394">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="6a487-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="6a487-395">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="6a487-396">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="6a487-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="6a487-397">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="6a487-397">Model binding automates this process.</span></span> <span data-ttu-id="6a487-398">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="6a487-398">The model binding system:</span></span>

* <span data-ttu-id="6a487-399">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="6a487-400">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="6a487-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="6a487-401">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="6a487-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="6a487-402">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="6a487-403">Пример</span><span class="sxs-lookup"><span data-stu-id="6a487-403">Example</span></span>

<span data-ttu-id="6a487-404">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="6a487-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="6a487-405">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="6a487-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="6a487-406">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="6a487-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="6a487-407">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="6a487-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="6a487-408">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="6a487-409">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="6a487-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="6a487-410">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="6a487-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="6a487-411">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="6a487-412">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="6a487-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="6a487-413">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="6a487-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="6a487-414">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="6a487-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="6a487-415">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="6a487-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="6a487-416">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="6a487-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="6a487-417">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="6a487-418">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="6a487-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="6a487-419">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="6a487-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="6a487-420">Цели</span><span class="sxs-lookup"><span data-stu-id="6a487-420">Targets</span></span>

<span data-ttu-id="6a487-421">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="6a487-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="6a487-422">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="6a487-423">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="6a487-424">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="6a487-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="6a487-425">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="6a487-425">[BindProperty] attribute</span></span>

<span data-ttu-id="6a487-426">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="6a487-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="6a487-427">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="6a487-427">[BindProperties] attribute</span></span>

<span data-ttu-id="6a487-428">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="6a487-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="6a487-429">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="6a487-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="6a487-430">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="6a487-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="6a487-431">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="6a487-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="6a487-432">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="6a487-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="6a487-433">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="6a487-434">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="6a487-435">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="6a487-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="6a487-436">Источники</span><span class="sxs-lookup"><span data-stu-id="6a487-436">Sources</span></span>

<span data-ttu-id="6a487-437">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="6a487-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="6a487-438">Поля формы</span><span class="sxs-lookup"><span data-stu-id="6a487-438">Form fields</span></span>
1. <span data-ttu-id="6a487-439">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="6a487-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="6a487-440">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="6a487-440">Route data</span></span>
1. <span data-ttu-id="6a487-441">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="6a487-441">Query string parameters</span></span>
1. <span data-ttu-id="6a487-442">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="6a487-442">Uploaded files</span></span>

<span data-ttu-id="6a487-443">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="6a487-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="6a487-444">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="6a487-444">There are a few exceptions:</span></span>

* <span data-ttu-id="6a487-445">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="6a487-446">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="6a487-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="6a487-447">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="6a487-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="6a487-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="6a487-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="6a487-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="6a487-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="6a487-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="6a487-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="6a487-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="6a487-453">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="6a487-453">These attributes:</span></span>

* <span data-ttu-id="6a487-454">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="6a487-455">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="6a487-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="6a487-456">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="6a487-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="6a487-457">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="6a487-458">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="6a487-458">[FromBody] attribute</span></span>

<span data-ttu-id="6a487-459">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="6a487-460">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="6a487-461">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="6a487-462">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="6a487-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="6a487-463">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="6a487-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="6a487-464">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="6a487-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="6a487-465">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a487-465">In the preceding example:</span></span>

* <span data-ttu-id="6a487-466">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="6a487-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="6a487-467">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="6a487-468">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="6a487-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="6a487-469">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="6a487-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="6a487-470">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="6a487-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="6a487-471">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6a487-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="6a487-472">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="6a487-472">Additional sources</span></span>

<span data-ttu-id="6a487-473">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="6a487-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="6a487-474">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="6a487-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="6a487-475">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="6a487-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="6a487-476">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="6a487-476">To get data from a new source:</span></span>

* <span data-ttu-id="6a487-477">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="6a487-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="6a487-478">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="6a487-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="6a487-479">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="6a487-480">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="6a487-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="6a487-481">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a487-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="6a487-482">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="6a487-483">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="6a487-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="6a487-484">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="6a487-484">No source for a model property</span></span>

<span data-ttu-id="6a487-485">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="6a487-486">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="6a487-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="6a487-487">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="6a487-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="6a487-488">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="6a487-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="6a487-489">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="6a487-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="6a487-490">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="6a487-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="6a487-491">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="6a487-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="6a487-492">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="6a487-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="6a487-493">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="6a487-494">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="6a487-495">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="6a487-495">Type conversion errors</span></span>

<span data-ttu-id="6a487-496">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="6a487-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="6a487-497">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="6a487-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="6a487-498">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="6a487-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="6a487-499">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="6a487-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="6a487-500">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="6a487-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="6a487-501">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="6a487-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="6a487-502">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="6a487-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="6a487-503">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="6a487-504">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="6a487-505">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6a487-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="6a487-506">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="6a487-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="6a487-507">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="6a487-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="6a487-508">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="6a487-509">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="6a487-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="6a487-510">Простые типы</span><span class="sxs-lookup"><span data-stu-id="6a487-510">Simple types</span></span>

<span data-ttu-id="6a487-511">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="6a487-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="6a487-512">Boolean</span><span class="sxs-lookup"><span data-stu-id="6a487-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="6a487-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="6a487-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="6a487-514">Char</span><span class="sxs-lookup"><span data-stu-id="6a487-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="6a487-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="6a487-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="6a487-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="6a487-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="6a487-517">Decimal</span><span class="sxs-lookup"><span data-stu-id="6a487-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="6a487-518">Double</span><span class="sxs-lookup"><span data-stu-id="6a487-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="6a487-519">Enum</span><span class="sxs-lookup"><span data-stu-id="6a487-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="6a487-520">Устройства</span><span class="sxs-lookup"><span data-stu-id="6a487-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="6a487-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="6a487-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="6a487-522">Single</span><span class="sxs-lookup"><span data-stu-id="6a487-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="6a487-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="6a487-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="6a487-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="6a487-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="6a487-525">URI</span><span class="sxs-lookup"><span data-stu-id="6a487-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="6a487-526">Version</span><span class="sxs-lookup"><span data-stu-id="6a487-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="6a487-527">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="6a487-527">Complex types</span></span>

<span data-ttu-id="6a487-528">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="6a487-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="6a487-529">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6a487-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="6a487-530">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="6a487-531">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="6a487-532">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="6a487-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="6a487-533">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="6a487-534">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="6a487-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="6a487-535">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="6a487-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="6a487-536">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="6a487-536">Prefix = parameter name</span></span>

<span data-ttu-id="6a487-537">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="6a487-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="6a487-538">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="6a487-539">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="6a487-540">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="6a487-540">Prefix = property name</span></span>

<span data-ttu-id="6a487-541">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6a487-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="6a487-542">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6a487-543">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="6a487-544">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="6a487-544">Custom prefix</span></span>

<span data-ttu-id="6a487-545">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="6a487-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="6a487-546">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="6a487-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6a487-547">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="6a487-548">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="6a487-548">Attributes for complex type targets</span></span>

<span data-ttu-id="6a487-549">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="6a487-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="6a487-550">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="6a487-551">Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="6a487-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="6a487-552">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="6a487-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="6a487-553">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="6a487-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="6a487-554">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="6a487-554">[BindRequired] attribute</span></span>

<span data-ttu-id="6a487-555">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6a487-556">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="6a487-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="6a487-557">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="6a487-558">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="6a487-558">[BindNever] attribute</span></span>

<span data-ttu-id="6a487-559">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6a487-560">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="6a487-561">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="6a487-562">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="6a487-562">[Bind] attribute</span></span>

<span data-ttu-id="6a487-563">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="6a487-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="6a487-564">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="6a487-565">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="6a487-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="6a487-566">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="6a487-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="6a487-567">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="6a487-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="6a487-568">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="6a487-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="6a487-569">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="6a487-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="6a487-570">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="6a487-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="6a487-571">Коллекции</span><span class="sxs-lookup"><span data-stu-id="6a487-571">Collections</span></span>

<span data-ttu-id="6a487-572">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6a487-573">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6a487-574">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-574">For example:</span></span>

* <span data-ttu-id="6a487-575">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="6a487-576">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="6a487-576">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="6a487-577">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="6a487-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="6a487-578">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6a487-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="6a487-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="6a487-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="6a487-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="6a487-581">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="6a487-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="6a487-582">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="6a487-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="6a487-583">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="6a487-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="6a487-584">Словари</span><span class="sxs-lookup"><span data-stu-id="6a487-584">Dictionaries</span></span>

<span data-ttu-id="6a487-585">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="6a487-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6a487-586">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="6a487-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6a487-587">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-587">For example:</span></span>

* <span data-ttu-id="6a487-588">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="6a487-589">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="6a487-589">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="6a487-590">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="6a487-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6a487-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="6a487-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="6a487-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="6a487-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="6a487-593">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="6a487-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="6a487-594">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6a487-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="6a487-595">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="6a487-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="6a487-596">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="6a487-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="6a487-597">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="6a487-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="6a487-598">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="6a487-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="6a487-599">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="6a487-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="6a487-600">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="6a487-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="6a487-601">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="6a487-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="6a487-602">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="6a487-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="6a487-603">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="6a487-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="6a487-604">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="6a487-604">Special data types</span></span>

<span data-ttu-id="6a487-605">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="6a487-606">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="6a487-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="6a487-607">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="6a487-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="6a487-608">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="6a487-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="6a487-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="6a487-609">CancellationToken</span></span>

<span data-ttu-id="6a487-610">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="6a487-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="6a487-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="6a487-611">FormCollection</span></span>

<span data-ttu-id="6a487-612">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="6a487-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="6a487-613">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="6a487-613">Input formatters</span></span>

<span data-ttu-id="6a487-614">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="6a487-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="6a487-615">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="6a487-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="6a487-616">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="6a487-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="6a487-617">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="6a487-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="6a487-618">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="6a487-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="6a487-619">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="6a487-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="6a487-620">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="6a487-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="6a487-621">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="6a487-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="6a487-622">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="6a487-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="6a487-623">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="6a487-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="6a487-624">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="6a487-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="6a487-625">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="6a487-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="6a487-626">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="6a487-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="6a487-627">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="6a487-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="6a487-628">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="6a487-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="6a487-629">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a487-630">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="6a487-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="6a487-631">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a487-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a487-632">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="6a487-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="6a487-633">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="6a487-633">Custom model binders</span></span>

<span data-ttu-id="6a487-634">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="6a487-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="6a487-635">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="6a487-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="6a487-636">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="6a487-636">Manual model binding</span></span>

<span data-ttu-id="6a487-637">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6a487-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="6a487-638">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="6a487-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="6a487-639">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="6a487-640">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6a487-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="6a487-641">Пример:</span><span class="sxs-lookup"><span data-stu-id="6a487-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="6a487-642">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="6a487-642">[FromServices] attribute</span></span>

<span data-ttu-id="6a487-643">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="6a487-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="6a487-644">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="6a487-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="6a487-645">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a487-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6a487-646">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="6a487-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a487-647">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6a487-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
