---
title: Привязка модели в ASP.NET Core
author: rick-anderson
description: Узнайте, как работает привязка модели в ASP.NET Core и как настроить ее поведение.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
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
ms.openlocfilehash: 6ec531a04a220f75f5793cb2c7b5232908dbd883
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019161"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="ffb58-103">Привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffb58-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ffb58-104">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="ffb58-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ffb58-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ffb58-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ffb58-106">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-106">What is Model binding</span></span>

<span data-ttu-id="ffb58-107">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ffb58-108">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ffb58-109">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="ffb58-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ffb58-110">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="ffb58-110">Model binding automates this process.</span></span> <span data-ttu-id="ffb58-111">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="ffb58-111">The model binding system:</span></span>

* <span data-ttu-id="ffb58-112">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ffb58-113">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ffb58-114">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="ffb58-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ffb58-115">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ffb58-116">Пример</span><span class="sxs-lookup"><span data-stu-id="ffb58-116">Example</span></span>

<span data-ttu-id="ffb58-117">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="ffb58-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ffb58-118">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="ffb58-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ffb58-119">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="ffb58-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ffb58-120">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ffb58-121">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ffb58-122">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="ffb58-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ffb58-123">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ffb58-124">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ffb58-125">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="ffb58-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ffb58-126">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ffb58-127">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ffb58-128">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="ffb58-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ffb58-129">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="ffb58-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ffb58-130">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ffb58-131">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="ffb58-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ffb58-132">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="ffb58-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ffb58-133">Целевые объекты</span><span class="sxs-lookup"><span data-stu-id="ffb58-133">Targets</span></span>

<span data-ttu-id="ffb58-134">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ffb58-135">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ffb58-136">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ffb58-137">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="ffb58-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ffb58-138">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="ffb58-138">[BindProperty] attribute</span></span>

<span data-ttu-id="ffb58-139">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="ffb58-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ffb58-140">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="ffb58-140">[BindProperties] attribute</span></span>

<span data-ttu-id="ffb58-141">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="ffb58-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ffb58-142">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="ffb58-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ffb58-143">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="ffb58-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ffb58-144">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="ffb58-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ffb58-145">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="ffb58-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ffb58-146">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ffb58-147">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ffb58-148">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ffb58-149">Источники</span><span class="sxs-lookup"><span data-stu-id="ffb58-149">Sources</span></span>

<span data-ttu-id="ffb58-150">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="ffb58-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ffb58-151">Поля формы</span><span class="sxs-lookup"><span data-stu-id="ffb58-151">Form fields</span></span>
1. <span data-ttu-id="ffb58-152">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="ffb58-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ffb58-153">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="ffb58-153">Route data</span></span>
1. <span data-ttu-id="ffb58-154">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="ffb58-154">Query string parameters</span></span>
1. <span data-ttu-id="ffb58-155">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="ffb58-155">Uploaded files</span></span>

<span data-ttu-id="ffb58-156">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ffb58-157">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="ffb58-157">There are a few exceptions:</span></span>

* <span data-ttu-id="ffb58-158">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ffb58-159">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ffb58-160">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="ffb58-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ffb58-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)— Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ffb58-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)— Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ffb58-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)— Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ffb58-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)— Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ffb58-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)— Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffb58-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ffb58-166">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="ffb58-166">These attributes:</span></span>

* <span data-ttu-id="ffb58-167">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ffb58-168">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ffb58-169">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ffb58-170">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ffb58-171">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="ffb58-171">[FromBody] attribute</span></span>

<span data-ttu-id="ffb58-172">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ffb58-173">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ffb58-174">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ffb58-175">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="ffb58-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ffb58-176">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="ffb58-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ffb58-177">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="ffb58-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ffb58-178">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-178">In the preceding example:</span></span>

* <span data-ttu-id="ffb58-179">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="ffb58-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ffb58-180">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ffb58-181">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="ffb58-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ffb58-182">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ffb58-183">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="ffb58-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ffb58-184">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ffb58-185">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="ffb58-185">Additional sources</span></span>

<span data-ttu-id="ffb58-186">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ffb58-187">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="ffb58-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ffb58-188">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ffb58-189">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="ffb58-189">To get data from a new source:</span></span>

* <span data-ttu-id="ffb58-190">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ffb58-191">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ffb58-192">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ffb58-193">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="ffb58-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ffb58-194">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="ffb58-195">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ffb58-196">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ffb58-197">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-197">No source for a model property</span></span>

<span data-ttu-id="ffb58-198">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ffb58-199">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="ffb58-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ffb58-200">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ffb58-201">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ffb58-202">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="ffb58-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ffb58-203">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="ffb58-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ffb58-204">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ffb58-205">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="ffb58-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ffb58-206">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ffb58-207">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ffb58-208">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="ffb58-208">Type conversion errors</span></span>

<span data-ttu-id="ffb58-209">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="ffb58-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ffb58-210">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ffb58-211">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="ffb58-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ffb58-212">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="ffb58-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ffb58-213">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="ffb58-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ffb58-214">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ffb58-215">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="ffb58-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ffb58-216">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ffb58-217">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ffb58-218">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ffb58-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ffb58-219">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ffb58-220">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="ffb58-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ffb58-221">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ffb58-222">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="ffb58-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ffb58-223">Простые типы</span><span class="sxs-lookup"><span data-stu-id="ffb58-223">Simple types</span></span>

<span data-ttu-id="ffb58-224">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="ffb58-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ffb58-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="ffb58-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ffb58-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ffb58-227">Char</span><span class="sxs-lookup"><span data-stu-id="ffb58-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ffb58-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="ffb58-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ffb58-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ffb58-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ffb58-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="ffb58-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ffb58-231">Double</span><span class="sxs-lookup"><span data-stu-id="ffb58-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ffb58-232">Перечисление</span><span class="sxs-lookup"><span data-stu-id="ffb58-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ffb58-233">Guid</span><span class="sxs-lookup"><span data-stu-id="ffb58-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ffb58-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ffb58-235">Single</span><span class="sxs-lookup"><span data-stu-id="ffb58-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ffb58-236">Временной</span><span class="sxs-lookup"><span data-stu-id="ffb58-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ffb58-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ffb58-238">URI</span><span class="sxs-lookup"><span data-stu-id="ffb58-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ffb58-239">Версия</span><span class="sxs-lookup"><span data-stu-id="ffb58-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ffb58-240">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="ffb58-240">Complex types</span></span>

<span data-ttu-id="ffb58-241">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ffb58-242">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ffb58-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ffb58-243">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ffb58-244">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ffb58-245">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="ffb58-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ffb58-246">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ffb58-247">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ffb58-248">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ffb58-249">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="ffb58-249">Prefix = parameter name</span></span>

<span data-ttu-id="ffb58-250">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ffb58-251">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ffb58-252">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ffb58-253">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="ffb58-253">Prefix = property name</span></span>

<span data-ttu-id="ffb58-254">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ffb58-255">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ffb58-256">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ffb58-257">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="ffb58-257">Custom prefix</span></span>

<span data-ttu-id="ffb58-258">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="ffb58-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ffb58-259">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ffb58-260">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ffb58-261">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="ffb58-261">Attributes for complex type targets</span></span>

<span data-ttu-id="ffb58-262">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="ffb58-263">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ffb58-264">Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="ffb58-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ffb58-265">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="ffb58-266">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="ffb58-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ffb58-267">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="ffb58-267">[BindRequired] attribute</span></span>

<span data-ttu-id="ffb58-268">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ffb58-269">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="ffb58-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ffb58-270">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="ffb58-271">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="ffb58-271">[BindNever] attribute</span></span>

<span data-ttu-id="ffb58-272">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ffb58-273">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ffb58-274">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="ffb58-275">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="ffb58-275">[Bind] attribute</span></span>

<span data-ttu-id="ffb58-276">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ffb58-277">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="ffb58-278">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="ffb58-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ffb58-279">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ffb58-280">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ffb58-281">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ffb58-282">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ffb58-283">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ffb58-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="ffb58-284">Коллекции</span><span class="sxs-lookup"><span data-stu-id="ffb58-284">Collections</span></span>

<span data-ttu-id="ffb58-285">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ffb58-286">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ffb58-287">Например:</span><span class="sxs-lookup"><span data-stu-id="ffb58-287">For example:</span></span>

* <span data-ttu-id="ffb58-288">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ffb58-289">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="ffb58-290">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="ffb58-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ffb58-291">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ffb58-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="ffb58-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ffb58-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="ffb58-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ffb58-294">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="ffb58-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ffb58-295">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="ffb58-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ffb58-296">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="ffb58-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ffb58-297">Словари</span><span class="sxs-lookup"><span data-stu-id="ffb58-297">Dictionaries</span></span>

<span data-ttu-id="ffb58-298">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ffb58-299">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ffb58-300">Например:</span><span class="sxs-lookup"><span data-stu-id="ffb58-300">For example:</span></span>

* <span data-ttu-id="ffb58-301">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ffb58-302">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="ffb58-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="ffb58-303">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ffb58-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ffb58-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ffb58-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="ffb58-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ffb58-306">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="ffb58-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ffb58-307">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ffb58-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ffb58-308">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="ffb58-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ffb58-309">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="ffb58-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ffb58-310">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="ffb58-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ffb58-311">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ffb58-312">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="ffb58-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ffb58-313">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ffb58-314">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="ffb58-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ffb58-315">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="ffb58-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ffb58-316">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="ffb58-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ffb58-317">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="ffb58-317">Special data types</span></span>

<span data-ttu-id="ffb58-318">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ffb58-319">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="ffb58-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ffb58-320">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ffb58-321">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ffb58-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ffb58-322">CancellationToken</span></span>

<span data-ttu-id="ffb58-323">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ffb58-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="ffb58-324">FormCollection</span></span>

<span data-ttu-id="ffb58-325">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ffb58-326">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="ffb58-326">Input formatters</span></span>

<span data-ttu-id="ffb58-327">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="ffb58-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ffb58-328">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="ffb58-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ffb58-329">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="ffb58-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ffb58-330">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="ffb58-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ffb58-331">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="ffb58-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ffb58-332">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="ffb58-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ffb58-333">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="ffb58-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ffb58-334">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ffb58-335">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="ffb58-336">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ffb58-337">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ffb58-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="ffb58-338">Настройка привязки модели с помощью форматировщиков входных данных</span><span class="sxs-lookup"><span data-stu-id="ffb58-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="ffb58-339">Форматировщик входных данных полностью отвечает за чтение данных из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="ffb58-340">Чтобы настроить этот процесс, настройте API-интерфейсы, используемые форматировщиками входных данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="ffb58-341">В этом разделе описывается, как настроить форматировщик входных данных на основе `System.Text.Json` так, чтобы он понимал настраиваемый тип с именем `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="ffb58-342">Рассмотрим следующую модель, которая содержит настраиваемое свойство `ObjectId` с именем `Id`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="ffb58-343">Чтобы настроить процесс привязки модели при использовании `System.Text.Json`, создайте производный класс на основе класса <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="ffb58-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="ffb58-344">Чтобы использовать пользовательский преобразователь, примените к типу атрибут <xref:System.Text.Json.Serialization.JsonConverterAttribute>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="ffb58-345">В следующем примере тип `ObjectId` настраивается с `ObjectIdConverter` в качестве пользовательского преобразователя:</span><span class="sxs-lookup"><span data-stu-id="ffb58-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="ffb58-346">Дополнительные сведения см. в статье [How to write custom converters for JSON serialization (marshalling) in .NET](/dotnet/standard/serialization/system-text-json-converters-how-to) (Создание пользовательских преобразователей для сериализации JSON (маршалинг) в .NET).</span><span class="sxs-lookup"><span data-stu-id="ffb58-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ffb58-347">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="ffb58-348">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="ffb58-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ffb58-349">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="ffb58-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ffb58-350">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ffb58-351">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ffb58-352">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="ffb58-353">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ffb58-354">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="ffb58-355">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-355">Custom model binders</span></span>

<span data-ttu-id="ffb58-356">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="ffb58-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ffb58-357">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="ffb58-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ffb58-358">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="ffb58-358">Manual model binding</span></span> 

<span data-ttu-id="ffb58-359">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ffb58-360">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ffb58-361">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ffb58-362">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ffb58-363">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="ffb58-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> использует поставщиков значений для получения данных из текста формы, строки запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="ffb58-365">`TryUpdateModelAsync` как правило:</span><span class="sxs-lookup"><span data-stu-id="ffb58-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="ffb58-366">Используется с Razor страницами и приложениями MVC с использованием контроллеров и представлений для предотвращения перебора.</span><span class="sxs-lookup"><span data-stu-id="ffb58-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="ffb58-367">не используется с веб-API, если только не используется из данных формы, строк запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="ffb58-368">Конечные точки веб-API, которые используют JSON, применяют [форматировщики входных данных](#input-formatters) для десериализации текста запроса в объект.</span><span class="sxs-lookup"><span data-stu-id="ffb58-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="ffb58-369">Дополнительные сведения см. в разделе [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="ffb58-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="ffb58-370">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="ffb58-370">[FromServices] attribute</span></span>

<span data-ttu-id="ffb58-371">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ffb58-372">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ffb58-373">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ffb58-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ffb58-374">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="ffb58-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffb58-375">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ffb58-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffb58-376">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="ffb58-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ffb58-377">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ffb58-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ffb58-378">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-378">What is Model binding</span></span>

<span data-ttu-id="ffb58-379">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ffb58-380">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ffb58-381">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="ffb58-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ffb58-382">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="ffb58-382">Model binding automates this process.</span></span> <span data-ttu-id="ffb58-383">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="ffb58-383">The model binding system:</span></span>

* <span data-ttu-id="ffb58-384">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ffb58-385">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ffb58-386">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="ffb58-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ffb58-387">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ffb58-388">Пример</span><span class="sxs-lookup"><span data-stu-id="ffb58-388">Example</span></span>

<span data-ttu-id="ffb58-389">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="ffb58-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ffb58-390">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="ffb58-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ffb58-391">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="ffb58-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ffb58-392">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ffb58-393">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ffb58-394">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="ffb58-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ffb58-395">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ffb58-396">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ffb58-397">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="ffb58-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ffb58-398">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ffb58-399">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ffb58-400">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="ffb58-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ffb58-401">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="ffb58-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ffb58-402">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ffb58-403">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="ffb58-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ffb58-404">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="ffb58-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ffb58-405">Целевые объекты</span><span class="sxs-lookup"><span data-stu-id="ffb58-405">Targets</span></span>

<span data-ttu-id="ffb58-406">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ffb58-407">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ffb58-408">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ffb58-409">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="ffb58-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ffb58-410">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="ffb58-410">[BindProperty] attribute</span></span>

<span data-ttu-id="ffb58-411">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="ffb58-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ffb58-412">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="ffb58-412">[BindProperties] attribute</span></span>

<span data-ttu-id="ffb58-413">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="ffb58-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ffb58-414">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="ffb58-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ffb58-415">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="ffb58-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ffb58-416">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="ffb58-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ffb58-417">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="ffb58-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ffb58-418">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ffb58-419">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ffb58-420">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ffb58-421">Источники</span><span class="sxs-lookup"><span data-stu-id="ffb58-421">Sources</span></span>

<span data-ttu-id="ffb58-422">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="ffb58-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ffb58-423">Поля формы</span><span class="sxs-lookup"><span data-stu-id="ffb58-423">Form fields</span></span>
1. <span data-ttu-id="ffb58-424">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="ffb58-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ffb58-425">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="ffb58-425">Route data</span></span>
1. <span data-ttu-id="ffb58-426">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="ffb58-426">Query string parameters</span></span>
1. <span data-ttu-id="ffb58-427">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="ffb58-427">Uploaded files</span></span>

<span data-ttu-id="ffb58-428">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ffb58-429">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="ffb58-429">There are a few exceptions:</span></span>

* <span data-ttu-id="ffb58-430">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ffb58-431">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ffb58-432">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="ffb58-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ffb58-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)— Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ffb58-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)— Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="ffb58-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ffb58-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)— Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ffb58-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)— Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ffb58-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)— Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffb58-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ffb58-438">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="ffb58-438">These attributes:</span></span>

* <span data-ttu-id="ffb58-439">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ffb58-440">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ffb58-441">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ffb58-442">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ffb58-443">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="ffb58-443">[FromBody] attribute</span></span>

<span data-ttu-id="ffb58-444">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ffb58-445">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ffb58-446">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ffb58-447">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="ffb58-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ffb58-448">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="ffb58-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ffb58-449">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="ffb58-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ffb58-450">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="ffb58-450">In the preceding example:</span></span>

* <span data-ttu-id="ffb58-451">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="ffb58-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ffb58-452">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ffb58-453">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="ffb58-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ffb58-454">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ffb58-455">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="ffb58-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ffb58-456">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ffb58-457">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="ffb58-457">Additional sources</span></span>

<span data-ttu-id="ffb58-458">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ffb58-459">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="ffb58-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ffb58-460">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ffb58-461">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="ffb58-461">To get data from a new source:</span></span>

* <span data-ttu-id="ffb58-462">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ffb58-463">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ffb58-464">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ffb58-465">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="ffb58-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ffb58-466">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="ffb58-467">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ffb58-468">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ffb58-469">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-469">No source for a model property</span></span>

<span data-ttu-id="ffb58-470">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ffb58-471">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="ffb58-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ffb58-472">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ffb58-473">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ffb58-474">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="ffb58-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ffb58-475">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="ffb58-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ffb58-476">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ffb58-477">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="ffb58-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ffb58-478">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ffb58-479">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ffb58-480">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="ffb58-480">Type conversion errors</span></span>

<span data-ttu-id="ffb58-481">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="ffb58-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ffb58-482">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="ffb58-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ffb58-483">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="ffb58-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ffb58-484">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="ffb58-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ffb58-485">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="ffb58-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ffb58-486">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ffb58-487">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="ffb58-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ffb58-488">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ffb58-489">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ffb58-490">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ffb58-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ffb58-491">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ffb58-492">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="ffb58-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ffb58-493">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ffb58-494">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="ffb58-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ffb58-495">Простые типы</span><span class="sxs-lookup"><span data-stu-id="ffb58-495">Simple types</span></span>

<span data-ttu-id="ffb58-496">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="ffb58-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ffb58-497">Boolean</span><span class="sxs-lookup"><span data-stu-id="ffb58-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ffb58-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ffb58-499">Char</span><span class="sxs-lookup"><span data-stu-id="ffb58-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ffb58-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="ffb58-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ffb58-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ffb58-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ffb58-502">Decimal</span><span class="sxs-lookup"><span data-stu-id="ffb58-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ffb58-503">Double</span><span class="sxs-lookup"><span data-stu-id="ffb58-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ffb58-504">Перечисление</span><span class="sxs-lookup"><span data-stu-id="ffb58-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ffb58-505">Guid</span><span class="sxs-lookup"><span data-stu-id="ffb58-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ffb58-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ffb58-507">Single</span><span class="sxs-lookup"><span data-stu-id="ffb58-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ffb58-508">Временной</span><span class="sxs-lookup"><span data-stu-id="ffb58-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ffb58-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ffb58-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ffb58-510">URI</span><span class="sxs-lookup"><span data-stu-id="ffb58-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ffb58-511">Версия</span><span class="sxs-lookup"><span data-stu-id="ffb58-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ffb58-512">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="ffb58-512">Complex types</span></span>

<span data-ttu-id="ffb58-513">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="ffb58-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ffb58-514">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ffb58-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ffb58-515">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ffb58-516">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ffb58-517">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="ffb58-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ffb58-518">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ffb58-519">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="ffb58-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ffb58-520">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ffb58-521">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="ffb58-521">Prefix = parameter name</span></span>

<span data-ttu-id="ffb58-522">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ffb58-523">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ffb58-524">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ffb58-525">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="ffb58-525">Prefix = property name</span></span>

<span data-ttu-id="ffb58-526">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ffb58-527">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ffb58-528">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ffb58-529">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="ffb58-529">Custom prefix</span></span>

<span data-ttu-id="ffb58-530">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="ffb58-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ffb58-531">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ffb58-532">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ffb58-533">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="ffb58-533">Attributes for complex type targets</span></span>

<span data-ttu-id="ffb58-534">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="ffb58-535">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ffb58-536">Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="ffb58-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ffb58-537">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="ffb58-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="ffb58-538">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="ffb58-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ffb58-539">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="ffb58-539">[BindRequired] attribute</span></span>

<span data-ttu-id="ffb58-540">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ffb58-541">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="ffb58-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ffb58-542">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="ffb58-543">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="ffb58-543">[BindNever] attribute</span></span>

<span data-ttu-id="ffb58-544">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ffb58-545">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ffb58-546">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="ffb58-547">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="ffb58-547">[Bind] attribute</span></span>

<span data-ttu-id="ffb58-548">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="ffb58-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ffb58-549">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="ffb58-550">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="ffb58-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ffb58-551">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ffb58-552">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ffb58-553">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ffb58-554">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ffb58-555">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ffb58-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="ffb58-556">Коллекции</span><span class="sxs-lookup"><span data-stu-id="ffb58-556">Collections</span></span>

<span data-ttu-id="ffb58-557">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ffb58-558">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ffb58-559">Например:</span><span class="sxs-lookup"><span data-stu-id="ffb58-559">For example:</span></span>

* <span data-ttu-id="ffb58-560">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ffb58-561">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="ffb58-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="ffb58-562">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="ffb58-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ffb58-563">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ffb58-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="ffb58-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ffb58-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="ffb58-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ffb58-566">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="ffb58-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ffb58-567">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="ffb58-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ffb58-568">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="ffb58-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ffb58-569">Словари</span><span class="sxs-lookup"><span data-stu-id="ffb58-569">Dictionaries</span></span>

<span data-ttu-id="ffb58-570">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="ffb58-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ffb58-571">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ffb58-572">Например:</span><span class="sxs-lookup"><span data-stu-id="ffb58-572">For example:</span></span>

* <span data-ttu-id="ffb58-573">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ffb58-574">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="ffb58-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="ffb58-575">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ffb58-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ffb58-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ffb58-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="ffb58-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ffb58-578">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="ffb58-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ffb58-579">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ffb58-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ffb58-580">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="ffb58-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ffb58-581">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="ffb58-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ffb58-582">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="ffb58-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ffb58-583">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ffb58-584">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="ffb58-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ffb58-585">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ffb58-586">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="ffb58-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ffb58-587">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="ffb58-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ffb58-588">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="ffb58-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ffb58-589">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="ffb58-589">Special data types</span></span>

<span data-ttu-id="ffb58-590">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ffb58-591">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="ffb58-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ffb58-592">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="ffb58-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ffb58-593">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ffb58-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ffb58-594">CancellationToken</span></span>

<span data-ttu-id="ffb58-595">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="ffb58-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ffb58-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="ffb58-596">FormCollection</span></span>

<span data-ttu-id="ffb58-597">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="ffb58-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ffb58-598">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="ffb58-598">Input formatters</span></span>

<span data-ttu-id="ffb58-599">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="ffb58-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ffb58-600">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="ffb58-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ffb58-601">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="ffb58-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ffb58-602">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="ffb58-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ffb58-603">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="ffb58-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ffb58-604">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="ffb58-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ffb58-605">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="ffb58-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ffb58-606">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ffb58-607">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="ffb58-608">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="ffb58-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ffb58-609">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ffb58-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ffb58-610">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="ffb58-611">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="ffb58-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ffb58-612">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="ffb58-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ffb58-613">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="ffb58-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ffb58-614">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ffb58-615">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="ffb58-616">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ffb58-617">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="ffb58-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="ffb58-618">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="ffb58-618">Custom model binders</span></span>

<span data-ttu-id="ffb58-619">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="ffb58-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ffb58-620">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="ffb58-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ffb58-621">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="ffb58-621">Manual model binding</span></span>

<span data-ttu-id="ffb58-622">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ffb58-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ffb58-623">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="ffb58-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ffb58-624">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ffb58-625">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="ffb58-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ffb58-626">Ниже приведен пример.</span><span class="sxs-lookup"><span data-stu-id="ffb58-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="ffb58-627">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="ffb58-627">[FromServices] attribute</span></span>

<span data-ttu-id="ffb58-628">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="ffb58-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ffb58-629">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="ffb58-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ffb58-630">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ffb58-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ffb58-631">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="ffb58-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffb58-632">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ffb58-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
