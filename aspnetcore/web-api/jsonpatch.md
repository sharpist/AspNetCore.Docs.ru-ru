---
<span data-ttu-id="6beaf-101">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-102">'Blazor'</span></span>
- <span data-ttu-id="6beaf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-103">'Identity'</span></span>
- <span data-ttu-id="6beaf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-105">'Razor'</span></span>
- <span data-ttu-id="6beaf-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="6beaf-107">JsonPatch в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beaf-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="6beaf-108">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Kirk Larkin](https://github.com/serpent5) (Кирк Ларкин)</span><span class="sxs-lookup"><span data-stu-id="6beaf-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6beaf-109">В этой статье описывается обработка запросов JSON Patch в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6beaf-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="6beaf-110">Установка пакета</span><span class="sxs-lookup"><span data-stu-id="6beaf-110">Package installation</span></span>

<span data-ttu-id="6beaf-111">Чтобы включить поддержку исправления JSON в приложении, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="6beaf-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="6beaf-112">Установите [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="6beaf-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="6beaf-113">Обновите метод проекта `Startup.ConfigureServices` для вызова <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="6beaf-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="6beaf-114">Пример:</span><span class="sxs-lookup"><span data-stu-id="6beaf-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="6beaf-115">`AddNewtonsoftJson` совместим с методами регистрации службы MVC:</span><span class="sxs-lookup"><span data-stu-id="6beaf-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="6beaf-116">Исправление JSON, Аддневтонсофтжсон и System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="6beaf-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="6beaf-117">`AddNewtonsoftJson`заменяет `System.Text.Json` модули форматирования ввода и вывода, используемые для форматирования **всего** содержимого JSON.</span><span class="sxs-lookup"><span data-stu-id="6beaf-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="6beaf-118">Чтобы добавить поддержку обновления JSON с помощью `Newtonsoft.Json` , при этом другие модули форматирования не меняются, обновите метод проекта следующим образом `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6beaf-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="6beaf-119">Для приведенного выше кода требуется `Microsoft.AspNetCore.Mvc.NewtonsoftJson` пакет и следующие `using` инструкции:</span><span class="sxs-lookup"><span data-stu-id="6beaf-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="6beaf-120">Метод HTTP-запроса PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-120">PATCH HTTP request method</span></span>

<span data-ttu-id="6beaf-121">Методы PUT и [PATCH](https://tools.ietf.org/html/rfc5789) используются для обновления существующего ресурса.</span><span class="sxs-lookup"><span data-stu-id="6beaf-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6beaf-122">Различие между ними заключается в том, что PUT заменяет весь ресурс, а PATCH указывает только изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6beaf-123">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-123">JSON Patch</span></span>

<span data-ttu-id="6beaf-124">Формат [JSON Patch](https://tools.ietf.org/html/rfc6902) используется для указания обновлений, применяемых к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="6beaf-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6beaf-125">Документ JSON Patch содержит массив *операций*.</span><span class="sxs-lookup"><span data-stu-id="6beaf-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6beaf-126">Каждая операция определяет конкретный тип изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="6beaf-127">Примерами таких изменений являются добавление элемента массива или замена значения свойства.</span><span class="sxs-lookup"><span data-stu-id="6beaf-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="6beaf-128">Например, следующие документы JSON представляют ресурс, документ исправления JSON для ресурса и результат применения операций исправления.</span><span class="sxs-lookup"><span data-stu-id="6beaf-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6beaf-129">Пример ресурса</span><span class="sxs-lookup"><span data-stu-id="6beaf-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6beaf-130">Пример JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6beaf-131">В приведенном выше документе JSON:</span><span class="sxs-lookup"><span data-stu-id="6beaf-131">In the preceding JSON:</span></span>

* <span data-ttu-id="6beaf-132">свойство `op` указывает тип операции;</span><span class="sxs-lookup"><span data-stu-id="6beaf-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6beaf-133">свойство `path` указывает обновляемый элемент;</span><span class="sxs-lookup"><span data-stu-id="6beaf-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6beaf-134">свойство `value` предоставляет новое значение.</span><span class="sxs-lookup"><span data-stu-id="6beaf-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6beaf-135">Ресурс после обновления</span><span class="sxs-lookup"><span data-stu-id="6beaf-135">Resource after patch</span></span>

<span data-ttu-id="6beaf-136">Ниже показан ресурс в том состоянии, которое он принимает после применения приведенного выше документа JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="6beaf-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="6beaf-137">Изменения, внесенные путем применения документа исправления JSON к ресурсу, являются атомарными.</span><span class="sxs-lookup"><span data-stu-id="6beaf-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="6beaf-138">Если какая-либо операция в списке завершается ошибкой, операция в списке не применяется.</span><span class="sxs-lookup"><span data-stu-id="6beaf-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6beaf-139">Синтаксис Path</span><span class="sxs-lookup"><span data-stu-id="6beaf-139">Path syntax</span></span>

<span data-ttu-id="6beaf-140">Свойство [Path](https://tools.ietf.org/html/rfc6901) в объекте операции содержит косые черты между уровнями.</span><span class="sxs-lookup"><span data-stu-id="6beaf-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6beaf-141">Например, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6beaf-142">Для указания элементов массива используются числовые индексы, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="6beaf-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6beaf-143">Первый элемент массива `addresses` будет обозначаться как `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6beaf-144">В `add` конец массива используйте дефис ( `-` ), а не номер индекса: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="6beaf-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6beaf-145">Операции</span><span class="sxs-lookup"><span data-stu-id="6beaf-145">Operations</span></span>

<span data-ttu-id="6beaf-146">В следующей таблице перечислены поддерживаемые операции, которые определены в [спецификации JSON Patch](https://tools.ietf.org/html/rfc6902).</span><span class="sxs-lookup"><span data-stu-id="6beaf-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6beaf-147">Операция</span><span class="sxs-lookup"><span data-stu-id="6beaf-147">Operation</span></span>  | <span data-ttu-id="6beaf-148">Примечания</span><span class="sxs-lookup"><span data-stu-id="6beaf-148">Notes</span></span> |
|---
<span data-ttu-id="6beaf-149">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-150">'Blazor'</span></span>
- <span data-ttu-id="6beaf-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-151">'Identity'</span></span>
- <span data-ttu-id="6beaf-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-153">'Razor'</span></span>
- <span data-ttu-id="6beaf-154">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-155">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-156">'Blazor'</span></span>
- <span data-ttu-id="6beaf-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-157">'Identity'</span></span>
- <span data-ttu-id="6beaf-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-159">'Razor'</span></span>
- <span data-ttu-id="6beaf-160">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-161">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-162">'Blazor'</span></span>
- <span data-ttu-id="6beaf-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-163">'Identity'</span></span>
- <span data-ttu-id="6beaf-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-165">'Razor'</span></span>
- <span data-ttu-id="6beaf-166">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-166">'SignalR' uid:</span></span> 

<span data-ttu-id="6beaf-167">------|---
Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-168">'Blazor'</span></span>
- <span data-ttu-id="6beaf-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-169">'Identity'</span></span>
- <span data-ttu-id="6beaf-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-171">'Razor'</span></span>
- <span data-ttu-id="6beaf-172">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-173">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-174">'Blazor'</span></span>
- <span data-ttu-id="6beaf-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-175">'Identity'</span></span>
- <span data-ttu-id="6beaf-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-177">'Razor'</span></span>
- <span data-ttu-id="6beaf-178">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-179">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-180">'Blazor'</span></span>
- <span data-ttu-id="6beaf-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-181">'Identity'</span></span>
- <span data-ttu-id="6beaf-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-183">'Razor'</span></span>
- <span data-ttu-id="6beaf-184">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-185">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-186">'Blazor'</span></span>
- <span data-ttu-id="6beaf-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-187">'Identity'</span></span>
- <span data-ttu-id="6beaf-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-189">'Razor'</span></span>
- <span data-ttu-id="6beaf-190">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-191">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-192">'Blazor'</span></span>
- <span data-ttu-id="6beaf-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-193">'Identity'</span></span>
- <span data-ttu-id="6beaf-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-195">'Razor'</span></span>
- <span data-ttu-id="6beaf-196">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-197">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-198">'Blazor'</span></span>
- <span data-ttu-id="6beaf-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-199">'Identity'</span></span>
- <span data-ttu-id="6beaf-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-201">'Razor'</span></span>
- <span data-ttu-id="6beaf-202">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-203">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-204">'Blazor'</span></span>
- <span data-ttu-id="6beaf-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-205">'Identity'</span></span>
- <span data-ttu-id="6beaf-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-207">'Razor'</span></span>
- <span data-ttu-id="6beaf-208">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-209">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-210">'Blazor'</span></span>
- <span data-ttu-id="6beaf-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-211">'Identity'</span></span>
- <span data-ttu-id="6beaf-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-213">'Razor'</span></span>
- <span data-ttu-id="6beaf-214">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-215">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-216">'Blazor'</span></span>
- <span data-ttu-id="6beaf-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-217">'Identity'</span></span>
- <span data-ttu-id="6beaf-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-219">'Razor'</span></span>
- <span data-ttu-id="6beaf-220">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-221">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-222">'Blazor'</span></span>
- <span data-ttu-id="6beaf-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-223">'Identity'</span></span>
- <span data-ttu-id="6beaf-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-225">'Razor'</span></span>
- <span data-ttu-id="6beaf-226">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-227">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-228">'Blazor'</span></span>
- <span data-ttu-id="6beaf-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-229">'Identity'</span></span>
- <span data-ttu-id="6beaf-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-231">'Razor'</span></span>
- <span data-ttu-id="6beaf-232">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-233">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-234">'Blazor'</span></span>
- <span data-ttu-id="6beaf-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-235">'Identity'</span></span>
- <span data-ttu-id="6beaf-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-237">'Razor'</span></span>
- <span data-ttu-id="6beaf-238">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-239">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-240">'Blazor'</span></span>
- <span data-ttu-id="6beaf-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-241">'Identity'</span></span>
- <span data-ttu-id="6beaf-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-243">'Razor'</span></span>
- <span data-ttu-id="6beaf-244">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-245">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-246">'Blazor'</span></span>
- <span data-ttu-id="6beaf-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-247">'Identity'</span></span>
- <span data-ttu-id="6beaf-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-249">'Razor'</span></span>
- <span data-ttu-id="6beaf-250">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-250">'SignalR' uid:</span></span> 

<span data-ttu-id="6beaf-251">----------------| | `add`     | Добавьте свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="6beaf-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="6beaf-252">Для существующего свойства: Set value. | | `remove`  | Удалите свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="6beaf-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="6beaf-253">| | `replace` | То же, что и `remove` , за `add` тем же расположением.</span><span class="sxs-lookup"><span data-stu-id="6beaf-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="6beaf-254">| | `move`    | То же, что и `remove` источник, за которым следует `add` назначение, используя значение из источника.</span><span class="sxs-lookup"><span data-stu-id="6beaf-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="6beaf-255">| | `copy`    | То же, что `add` и назначение, используя значение из источника.</span><span class="sxs-lookup"><span data-stu-id="6beaf-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="6beaf-256">| | `test`    | Вернуть код состояния успеха, если указано значение в параметре `path` = `value` . |</span><span class="sxs-lookup"><span data-stu-id="6beaf-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="6beaf-257">Обновление JSON в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beaf-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="6beaf-258">Реализация ASP.NET Core для JSON Patch предоставляется в пакете NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="6beaf-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6beaf-259">Код метода действия</span><span class="sxs-lookup"><span data-stu-id="6beaf-259">Action method code</span></span>

<span data-ttu-id="6beaf-260">В контроллере API есть метод действия для JSON Patch, который:</span><span class="sxs-lookup"><span data-stu-id="6beaf-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6beaf-261">помечен атрибутом `HttpPatch`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6beaf-262">принимает `JsonPatchDocument<T>` обычно с указанием `[FromBody]`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6beaf-263">вызывает `ApplyTo` для целевого документа, чтобы применить изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6beaf-264">Приведем пример:</span><span class="sxs-lookup"><span data-stu-id="6beaf-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6beaf-265">Этот код из примера приложения работает со следующей `Customer` моделью:</span><span class="sxs-lookup"><span data-stu-id="6beaf-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6beaf-266">Этот пример метода действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-266">The sample action method:</span></span>

* <span data-ttu-id="6beaf-267">Создает документ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6beaf-268">применяет изменения;</span><span class="sxs-lookup"><span data-stu-id="6beaf-268">Applies the patch.</span></span>
* <span data-ttu-id="6beaf-269">возвращает результат в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="6beaf-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="6beaf-270">В реальном приложении код будет извлекать данные из хранилища, например из базы данных, и обновлять эту базу данных после применения исправлений.</span><span class="sxs-lookup"><span data-stu-id="6beaf-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6beaf-271">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="6beaf-271">Model state</span></span>

<span data-ttu-id="6beaf-272">Указанный выше пример метода действия вызывает перегрузку `ApplyTo`, которая принимает состояние модели в качестве одного из параметров.</span><span class="sxs-lookup"><span data-stu-id="6beaf-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6beaf-273">В этом случае вы получаете в ответах сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="6beaf-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6beaf-274">В следующем примере показан текст ответа с кодом 400 "Неверный запрос" для операции `test`:</span><span class="sxs-lookup"><span data-stu-id="6beaf-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6beaf-275">Динамические объекты</span><span class="sxs-lookup"><span data-stu-id="6beaf-275">Dynamic objects</span></span>

<span data-ttu-id="6beaf-276">В следующем примере метода действия показано, как применить исправление к динамическому объекту.</span><span class="sxs-lookup"><span data-stu-id="6beaf-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6beaf-277">Операция добавления</span><span class="sxs-lookup"><span data-stu-id="6beaf-277">The add operation</span></span>

* <span data-ttu-id="6beaf-278">Если `path` указывает на элемент массива, новый элемент вставляется перед тем, который указан в параметре `path`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6beaf-279">Если `path` указывает на свойство, задается значение свойства.</span><span class="sxs-lookup"><span data-stu-id="6beaf-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6beaf-280">Если `path` указывает на несуществующее расположение:</span><span class="sxs-lookup"><span data-stu-id="6beaf-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6beaf-281">Если обновляемый ресурс является динамическим объектом, добавляется свойство.</span><span class="sxs-lookup"><span data-stu-id="6beaf-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6beaf-282">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6beaf-283">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и добавляет объект `Order` в конец массива `Orders`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6beaf-284">Операция удаления</span><span class="sxs-lookup"><span data-stu-id="6beaf-284">The remove operation</span></span>

* <span data-ttu-id="6beaf-285">Если `path` указывает на элемент массива, элемент удаляется.</span><span class="sxs-lookup"><span data-stu-id="6beaf-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6beaf-286">Если `path` указывает на свойство:</span><span class="sxs-lookup"><span data-stu-id="6beaf-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="6beaf-287">Если обновляемый ресурс является динамическим объектом, свойство удаляется.</span><span class="sxs-lookup"><span data-stu-id="6beaf-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6beaf-288">Если обновляемый ресурс является статическим объектом:</span><span class="sxs-lookup"><span data-stu-id="6beaf-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6beaf-289">Если свойство может принимать значения NULL, ему присваивается значение NULL.</span><span class="sxs-lookup"><span data-stu-id="6beaf-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6beaf-290">Если свойство не может принимать значения NULL, ему присваивается значение `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6beaf-291">В следующем образце документа исправления устанавливается `CustomerName` значение NULL и удаляются `Orders[0]` следующие значения:</span><span class="sxs-lookup"><span data-stu-id="6beaf-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6beaf-292">Операция замены</span><span class="sxs-lookup"><span data-stu-id="6beaf-292">The replace operation</span></span>

<span data-ttu-id="6beaf-293">Эта операция функционально идентична операции `remove` с последующей `add`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6beaf-294">В следующем образце документа исправления значение параметра `CustomerName` и заменяется `Orders[0]` новым `Order` объектом:</span><span class="sxs-lookup"><span data-stu-id="6beaf-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6beaf-295">Операция перемещения</span><span class="sxs-lookup"><span data-stu-id="6beaf-295">The move operation</span></span>

* <span data-ttu-id="6beaf-296">Если `path` указывает на элемент массива, элемент `from` копируется в расположение элемента `path`, а затем выполняется операция `remove` для элемента `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6beaf-297">Если `path` указывает на свойство, значение свойства `from` копируется в свойство `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6beaf-298">Если `path` указывает на несуществующее свойство:</span><span class="sxs-lookup"><span data-stu-id="6beaf-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6beaf-299">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6beaf-300">Если исправляемый ресурс является динамическим объектом, значение `from` копируется в местоположение, указанное параметром `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6beaf-301">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-301">The following sample patch document:</span></span>

* <span data-ttu-id="6beaf-302">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6beaf-303">присваивает `Orders[0].OrderName` значение NULL;</span><span class="sxs-lookup"><span data-stu-id="6beaf-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6beaf-304">перемещает `Orders[1]` в расположение перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6beaf-305">Операция копирования</span><span class="sxs-lookup"><span data-stu-id="6beaf-305">The copy operation</span></span>

<span data-ttu-id="6beaf-306">Эта операция функционально аналогична операции `move` без последнего шага `remove`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6beaf-307">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-307">The following sample patch document:</span></span>

* <span data-ttu-id="6beaf-308">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6beaf-309">вставляет копию `Orders[1]` перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6beaf-310">Операция тестирования</span><span class="sxs-lookup"><span data-stu-id="6beaf-310">The test operation</span></span>

<span data-ttu-id="6beaf-311">Если значение в расположении, которое задано параметром `path`, отличается от предоставленного в `value` значения, запрос завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6beaf-312">В этом случае сбой распространяется на весь запрос PATCH, даже если все остальные операции в документе с исправлениями могли быть выполнены успешно.</span><span class="sxs-lookup"><span data-stu-id="6beaf-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6beaf-313">Операция `test` традиционно используется для предотвращения обновлений при возможных конфликтах параллелизма.</span><span class="sxs-lookup"><span data-stu-id="6beaf-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6beaf-314">Следующий пример документа с исправлениями не изменяет прежнее значение `CustomerName` (John), так как тест завершается ошибкой:</span><span class="sxs-lookup"><span data-stu-id="6beaf-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6beaf-315">Получение кода</span><span class="sxs-lookup"><span data-stu-id="6beaf-315">Get the code</span></span>

<span data-ttu-id="6beaf-316">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="6beaf-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="6beaf-317">([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="6beaf-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6beaf-318">Чтобы проверить этот пример, запустите приложение и отправьте HTTP-запросы со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="6beaf-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6beaf-319">URL-адрес: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6beaf-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6beaf-320">Метод HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6beaf-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6beaf-321">Заголовок: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6beaf-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6beaf-322">Текст: Скопируйте и вставьте один из примеров документа JSON исправления из папки проекта *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6beaf-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6beaf-323">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6beaf-323">Additional resources</span></span>

* [<span data-ttu-id="6beaf-324">Спецификация IETF RFC 5789 для метода PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6beaf-325">Спецификация IETF RFC 6902 для JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6beaf-326">Спецификация IETF RFC 6901 для формата Path в JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6beaf-327">[Документация по JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6beaf-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6beaf-328">Содержит ссылки на ресурсы для создания документов JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="6beaf-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6beaf-329">Исходный код JSON Patch для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beaf-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6beaf-330">В этой статье описывается обработка запросов JSON Patch в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6beaf-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="6beaf-331">Метод HTTP-запроса PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-331">PATCH HTTP request method</span></span>

<span data-ttu-id="6beaf-332">Методы PUT и [PATCH](https://tools.ietf.org/html/rfc5789) используются для обновления существующего ресурса.</span><span class="sxs-lookup"><span data-stu-id="6beaf-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6beaf-333">Различие между ними заключается в том, что PUT заменяет весь ресурс, а PATCH указывает только изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6beaf-334">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-334">JSON Patch</span></span>

<span data-ttu-id="6beaf-335">Формат [JSON Patch](https://tools.ietf.org/html/rfc6902) используется для указания обновлений, применяемых к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="6beaf-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6beaf-336">Документ JSON Patch содержит массив *операций*.</span><span class="sxs-lookup"><span data-stu-id="6beaf-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6beaf-337">Каждая операция обозначает определенный тип изменений, например добавление элемента массива или замену значения свойства.</span><span class="sxs-lookup"><span data-stu-id="6beaf-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="6beaf-338">Так, следующие документы JSON описывают ресурс, сведения JSON Patch для изменения этого ресурса и результат применения операций изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6beaf-339">Пример ресурса</span><span class="sxs-lookup"><span data-stu-id="6beaf-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6beaf-340">Пример JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6beaf-341">В приведенном выше документе JSON:</span><span class="sxs-lookup"><span data-stu-id="6beaf-341">In the preceding JSON:</span></span>

* <span data-ttu-id="6beaf-342">свойство `op` указывает тип операции;</span><span class="sxs-lookup"><span data-stu-id="6beaf-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6beaf-343">свойство `path` указывает обновляемый элемент;</span><span class="sxs-lookup"><span data-stu-id="6beaf-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6beaf-344">свойство `value` предоставляет новое значение.</span><span class="sxs-lookup"><span data-stu-id="6beaf-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6beaf-345">Ресурс после обновления</span><span class="sxs-lookup"><span data-stu-id="6beaf-345">Resource after patch</span></span>

<span data-ttu-id="6beaf-346">Ниже показан ресурс в том состоянии, которое он принимает после применения приведенного выше документа JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="6beaf-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="6beaf-347">Изменения, внесенные при применении к ресурсу документа JSON Patch, являются атомарными, то есть ни одна из операций не будет применена, если любая из них завершится сбоем.</span><span class="sxs-lookup"><span data-stu-id="6beaf-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6beaf-348">Синтаксис Path</span><span class="sxs-lookup"><span data-stu-id="6beaf-348">Path syntax</span></span>

<span data-ttu-id="6beaf-349">Свойство [Path](https://tools.ietf.org/html/rfc6901) в объекте операции содержит косые черты между уровнями.</span><span class="sxs-lookup"><span data-stu-id="6beaf-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6beaf-350">Например, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6beaf-351">Для указания элементов массива используются числовые индексы, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="6beaf-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6beaf-352">Первый элемент массива `addresses` будет обозначаться как `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6beaf-353">Чтобы применить `add` для последнего элемента массива, укажите дефис (-) вместо номера индекса: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6beaf-354">Операции</span><span class="sxs-lookup"><span data-stu-id="6beaf-354">Operations</span></span>

<span data-ttu-id="6beaf-355">В следующей таблице перечислены поддерживаемые операции, которые определены в [спецификации JSON Patch](https://tools.ietf.org/html/rfc6902).</span><span class="sxs-lookup"><span data-stu-id="6beaf-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6beaf-356">Операция</span><span class="sxs-lookup"><span data-stu-id="6beaf-356">Operation</span></span>  | <span data-ttu-id="6beaf-357">Примечания</span><span class="sxs-lookup"><span data-stu-id="6beaf-357">Notes</span></span> |
|---
<span data-ttu-id="6beaf-358">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-359">'Blazor'</span></span>
- <span data-ttu-id="6beaf-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-360">'Identity'</span></span>
- <span data-ttu-id="6beaf-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-362">'Razor'</span></span>
- <span data-ttu-id="6beaf-363">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-364">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-365">'Blazor'</span></span>
- <span data-ttu-id="6beaf-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-366">'Identity'</span></span>
- <span data-ttu-id="6beaf-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-368">'Razor'</span></span>
- <span data-ttu-id="6beaf-369">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-370">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-371">'Blazor'</span></span>
- <span data-ttu-id="6beaf-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-372">'Identity'</span></span>
- <span data-ttu-id="6beaf-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-374">'Razor'</span></span>
- <span data-ttu-id="6beaf-375">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-375">'SignalR' uid:</span></span> 

<span data-ttu-id="6beaf-376">------|---
Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-377">'Blazor'</span></span>
- <span data-ttu-id="6beaf-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-378">'Identity'</span></span>
- <span data-ttu-id="6beaf-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-380">'Razor'</span></span>
- <span data-ttu-id="6beaf-381">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-382">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-383">'Blazor'</span></span>
- <span data-ttu-id="6beaf-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-384">'Identity'</span></span>
- <span data-ttu-id="6beaf-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-386">'Razor'</span></span>
- <span data-ttu-id="6beaf-387">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-388">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-389">'Blazor'</span></span>
- <span data-ttu-id="6beaf-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-390">'Identity'</span></span>
- <span data-ttu-id="6beaf-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-392">'Razor'</span></span>
- <span data-ttu-id="6beaf-393">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-394">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-395">'Blazor'</span></span>
- <span data-ttu-id="6beaf-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-396">'Identity'</span></span>
- <span data-ttu-id="6beaf-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-398">'Razor'</span></span>
- <span data-ttu-id="6beaf-399">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-400">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-401">'Blazor'</span></span>
- <span data-ttu-id="6beaf-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-402">'Identity'</span></span>
- <span data-ttu-id="6beaf-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-404">'Razor'</span></span>
- <span data-ttu-id="6beaf-405">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-406">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-407">'Blazor'</span></span>
- <span data-ttu-id="6beaf-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-408">'Identity'</span></span>
- <span data-ttu-id="6beaf-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-410">'Razor'</span></span>
- <span data-ttu-id="6beaf-411">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-412">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-413">'Blazor'</span></span>
- <span data-ttu-id="6beaf-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-414">'Identity'</span></span>
- <span data-ttu-id="6beaf-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-416">'Razor'</span></span>
- <span data-ttu-id="6beaf-417">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-418">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-419">'Blazor'</span></span>
- <span data-ttu-id="6beaf-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-420">'Identity'</span></span>
- <span data-ttu-id="6beaf-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-422">'Razor'</span></span>
- <span data-ttu-id="6beaf-423">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-424">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-425">'Blazor'</span></span>
- <span data-ttu-id="6beaf-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-426">'Identity'</span></span>
- <span data-ttu-id="6beaf-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-428">'Razor'</span></span>
- <span data-ttu-id="6beaf-429">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-430">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-431">'Blazor'</span></span>
- <span data-ttu-id="6beaf-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-432">'Identity'</span></span>
- <span data-ttu-id="6beaf-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-434">'Razor'</span></span>
- <span data-ttu-id="6beaf-435">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-436">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-437">'Blazor'</span></span>
- <span data-ttu-id="6beaf-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-438">'Identity'</span></span>
- <span data-ttu-id="6beaf-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-440">'Razor'</span></span>
- <span data-ttu-id="6beaf-441">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-442">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-443">'Blazor'</span></span>
- <span data-ttu-id="6beaf-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-444">'Identity'</span></span>
- <span data-ttu-id="6beaf-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-446">'Razor'</span></span>
- <span data-ttu-id="6beaf-447">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-448">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-449">'Blazor'</span></span>
- <span data-ttu-id="6beaf-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-450">'Identity'</span></span>
- <span data-ttu-id="6beaf-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-452">'Razor'</span></span>
- <span data-ttu-id="6beaf-453">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6beaf-454">Заголовок: Автор: описание: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6beaf-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6beaf-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-455">'Blazor'</span></span>
- <span data-ttu-id="6beaf-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6beaf-456">'Identity'</span></span>
- <span data-ttu-id="6beaf-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6beaf-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="6beaf-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6beaf-458">'Razor'</span></span>
- <span data-ttu-id="6beaf-459">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="6beaf-459">'SignalR' uid:</span></span> 

<span data-ttu-id="6beaf-460">----------------| | `add`     | Добавьте свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="6beaf-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="6beaf-461">Для существующего свойства: Set value. | | `remove`  | Удалите свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="6beaf-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="6beaf-462">| | `replace` | То же, что и `remove` , за `add` тем же расположением.</span><span class="sxs-lookup"><span data-stu-id="6beaf-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="6beaf-463">| | `move`    | То же, что и `remove` источник, за которым следует `add` назначение, используя значение из источника.</span><span class="sxs-lookup"><span data-stu-id="6beaf-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="6beaf-464">| | `copy`    | То же, что `add` и назначение, используя значение из источника.</span><span class="sxs-lookup"><span data-stu-id="6beaf-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="6beaf-465">| | `test`    | Вернуть код состояния успеха, если указано значение в параметре `path` = `value` . |</span><span class="sxs-lookup"><span data-stu-id="6beaf-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="6beaf-466">JsonPatch в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beaf-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="6beaf-467">Реализация ASP.NET Core для JSON Patch предоставляется в пакете NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="6beaf-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="6beaf-468">Этот пакет входит в состав метапакета [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6beaf-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6beaf-469">Код метода действия</span><span class="sxs-lookup"><span data-stu-id="6beaf-469">Action method code</span></span>

<span data-ttu-id="6beaf-470">В контроллере API есть метод действия для JSON Patch, который:</span><span class="sxs-lookup"><span data-stu-id="6beaf-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6beaf-471">помечен атрибутом `HttpPatch`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6beaf-472">принимает `JsonPatchDocument<T>` обычно с указанием `[FromBody]`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6beaf-473">вызывает `ApplyTo` для целевого документа, чтобы применить изменения.</span><span class="sxs-lookup"><span data-stu-id="6beaf-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6beaf-474">Приведем пример:</span><span class="sxs-lookup"><span data-stu-id="6beaf-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6beaf-475">Этот код из примера приложения работает со следующей моделью `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6beaf-476">Этот пример метода действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-476">The sample action method:</span></span>

* <span data-ttu-id="6beaf-477">Создает документ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6beaf-478">применяет изменения;</span><span class="sxs-lookup"><span data-stu-id="6beaf-478">Applies the patch.</span></span>
* <span data-ttu-id="6beaf-479">возвращает результат в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="6beaf-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="6beaf-480">В реальном приложении код будет извлекать данные из хранилища, например из базы данных, и обновлять эту базу данных после применения исправлений.</span><span class="sxs-lookup"><span data-stu-id="6beaf-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6beaf-481">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="6beaf-481">Model state</span></span>

<span data-ttu-id="6beaf-482">Указанный выше пример метода действия вызывает перегрузку `ApplyTo`, которая принимает состояние модели в качестве одного из параметров.</span><span class="sxs-lookup"><span data-stu-id="6beaf-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6beaf-483">В этом случае вы получаете в ответах сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="6beaf-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6beaf-484">В следующем примере показан текст ответа с кодом 400 "Неверный запрос" для операции `test`:</span><span class="sxs-lookup"><span data-stu-id="6beaf-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6beaf-485">Динамические объекты</span><span class="sxs-lookup"><span data-stu-id="6beaf-485">Dynamic objects</span></span>

<span data-ttu-id="6beaf-486">Следующий пример метода действия демонстрирует, как применить исправление к динамическому объекту.</span><span class="sxs-lookup"><span data-stu-id="6beaf-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6beaf-487">Операция добавления</span><span class="sxs-lookup"><span data-stu-id="6beaf-487">The add operation</span></span>

* <span data-ttu-id="6beaf-488">Если `path` указывает на элемент массива, новый элемент вставляется перед тем, который указан в параметре `path`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6beaf-489">Если `path` указывает на свойство, задается значение свойства.</span><span class="sxs-lookup"><span data-stu-id="6beaf-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6beaf-490">Если `path` указывает на несуществующее расположение:</span><span class="sxs-lookup"><span data-stu-id="6beaf-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6beaf-491">Если обновляемый ресурс является динамическим объектом, добавляется свойство.</span><span class="sxs-lookup"><span data-stu-id="6beaf-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6beaf-492">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6beaf-493">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и добавляет объект `Order` в конец массива `Orders`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6beaf-494">Операция удаления</span><span class="sxs-lookup"><span data-stu-id="6beaf-494">The remove operation</span></span>

* <span data-ttu-id="6beaf-495">Если `path` указывает на элемент массива, элемент удаляется.</span><span class="sxs-lookup"><span data-stu-id="6beaf-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6beaf-496">Если `path` указывает на свойство:</span><span class="sxs-lookup"><span data-stu-id="6beaf-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="6beaf-497">Если обновляемый ресурс является динамическим объектом, свойство удаляется.</span><span class="sxs-lookup"><span data-stu-id="6beaf-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6beaf-498">Если обновляемый ресурс является статическим объектом:</span><span class="sxs-lookup"><span data-stu-id="6beaf-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6beaf-499">Если свойство может принимать значения NULL, ему присваивается значение NULL.</span><span class="sxs-lookup"><span data-stu-id="6beaf-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6beaf-500">Если свойство не может принимать значения NULL, ему присваивается значение `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6beaf-501">Следующий пример документа с исправлениями присваивает `CustomerName` значение NULL и удаляет `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6beaf-502">Операция замены</span><span class="sxs-lookup"><span data-stu-id="6beaf-502">The replace operation</span></span>

<span data-ttu-id="6beaf-503">Эта операция функционально идентична операции `remove` с последующей `add`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6beaf-504">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и заменяет `Orders[0]` новым объектом `Order`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6beaf-505">Операция перемещения</span><span class="sxs-lookup"><span data-stu-id="6beaf-505">The move operation</span></span>

* <span data-ttu-id="6beaf-506">Если `path` указывает на элемент массива, элемент `from` копируется в расположение элемента `path`, а затем выполняется операция `remove` для элемента `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6beaf-507">Если `path` указывает на свойство, значение свойства `from` копируется в свойство `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6beaf-508">Если `path` указывает на несуществующее свойство:</span><span class="sxs-lookup"><span data-stu-id="6beaf-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6beaf-509">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6beaf-510">Если исправляемый ресурс является динамическим объектом, значение `from` копируется в местоположение, указанное параметром `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6beaf-511">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-511">The following sample patch document:</span></span>

* <span data-ttu-id="6beaf-512">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6beaf-513">присваивает `Orders[0].OrderName` значение NULL;</span><span class="sxs-lookup"><span data-stu-id="6beaf-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6beaf-514">перемещает `Orders[1]` в расположение перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6beaf-515">Операция копирования</span><span class="sxs-lookup"><span data-stu-id="6beaf-515">The copy operation</span></span>

<span data-ttu-id="6beaf-516">Эта операция функционально аналогична операции `move` без последнего шага `remove`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6beaf-517">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="6beaf-517">The following sample patch document:</span></span>

* <span data-ttu-id="6beaf-518">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="6beaf-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6beaf-519">вставляет копию `Orders[1]` перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6beaf-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6beaf-520">Операция тестирования</span><span class="sxs-lookup"><span data-stu-id="6beaf-520">The test operation</span></span>

<span data-ttu-id="6beaf-521">Если значение в расположении, которое задано параметром `path`, отличается от предоставленного в `value` значения, запрос завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6beaf-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6beaf-522">В этом случае сбой распространяется на весь запрос PATCH, даже если все остальные операции в документе с исправлениями могли быть выполнены успешно.</span><span class="sxs-lookup"><span data-stu-id="6beaf-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6beaf-523">Операция `test` традиционно используется для предотвращения обновлений при возможных конфликтах параллелизма.</span><span class="sxs-lookup"><span data-stu-id="6beaf-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6beaf-524">Следующий пример документа с исправлениями не изменяет прежнее значение `CustomerName` (John), так как тест завершается ошибкой:</span><span class="sxs-lookup"><span data-stu-id="6beaf-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6beaf-525">Получение кода</span><span class="sxs-lookup"><span data-stu-id="6beaf-525">Get the code</span></span>

<span data-ttu-id="6beaf-526">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="6beaf-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="6beaf-527">([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="6beaf-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6beaf-528">Чтобы проверить этот пример, запустите приложение и отправьте HTTP-запросы со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="6beaf-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6beaf-529">URL-адрес: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6beaf-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6beaf-530">Метод HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6beaf-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6beaf-531">Заголовок: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6beaf-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6beaf-532">Текст: Скопируйте и вставьте один из примеров документа JSON исправления из папки проекта *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6beaf-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6beaf-533">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6beaf-533">Additional resources</span></span>

* [<span data-ttu-id="6beaf-534">Спецификация IETF RFC 5789 для метода PATCH</span><span class="sxs-lookup"><span data-stu-id="6beaf-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6beaf-535">Спецификация IETF RFC 6902 для JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6beaf-536">Спецификация IETF RFC 6901 для формата Path в JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6beaf-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6beaf-537">[Документация по JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6beaf-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6beaf-538">Содержит ссылки на ресурсы для создания документов JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="6beaf-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6beaf-539">Исходный код JSON Patch для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6beaf-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
