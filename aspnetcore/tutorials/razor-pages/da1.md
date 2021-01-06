---
title: Часть 5. Обновление созданных страниц
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages.
ms.author: riande
ms.date: 09/20/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 46fbfb50afd03f918f9e02bcc8c1dbde9a080ca4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485944"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="1b157-103">Часть 5. Изменение созданных страниц в приложении ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b157-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="1b157-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1b157-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b157-105">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="1b157-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="1b157-106">Вместо **ReleaseDate** должно стоять **Дата выпуска**, то есть два слова.</span><span class="sxs-lookup"><span data-stu-id="1b157-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="1b157-108">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="1b157-108">Update the generated code</span></span>

<span data-ttu-id="1b157-109">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="1b157-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="1b157-110">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="1b157-110">In the previous code:</span></span>

* <span data-ttu-id="1b157-111">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1b157-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="1b157-112">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="1b157-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="1b157-113">Атрибут [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) указывает на отображаемое имя поля.</span><span class="sxs-lookup"><span data-stu-id="1b157-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="1b157-114">В приведенном выше коде ReleaseDate заменен на "Дата выпуска".</span><span class="sxs-lookup"><span data-stu-id="1b157-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="1b157-115">Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="1b157-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="1b157-116">Сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="1b157-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="1b157-117">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="1b157-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="1b157-118">Перейдите к *Pages/Movies* и наведите указатель мыши на ссылку **Edit** (Изменение), чтобы просмотреть целевой URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="1b157-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="1b157-120">Ссылки **Edit**, **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1b157-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="1b157-121">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="1b157-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="1b157-122">В приведенном выше коде [Вспомогательная функция привязки тегов](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) динамически создает значение атрибута HTML `href` на основе Razor Page (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="1b157-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="1b157-123">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="1b157-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="1b157-124">Для проверки созданной разметки используйте в браузере параметр **Просмотреть исходный код**.</span><span class="sxs-lookup"><span data-stu-id="1b157-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="1b157-125">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="1b157-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="1b157-126">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса.</span><span class="sxs-lookup"><span data-stu-id="1b157-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="1b157-127">Например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="1b157-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="1b157-128">Добавление шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="1b157-128">Add route template</span></span>

<span data-ttu-id="1b157-129">Обновите страницы Razor Pages Edit, Details и Delete так, чтобы использовался шаблон маршрута `{id:int}`.</span><span class="sxs-lookup"><span data-stu-id="1b157-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="1b157-130">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="1b157-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="1b157-131">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="1b157-131">Run the app and then view source.</span></span>

<span data-ttu-id="1b157-132">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="1b157-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="1b157-133">Запрос к странице с шаблоном маршрута `{id:int}`, который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="1b157-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1b157-134">Например, `https://localhost:5001/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="1b157-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="1b157-135">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="1b157-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1b157-136">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="1b157-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="1b157-137">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1b157-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="1b157-138">Установите точку останова`public async Task<IActionResult> OnGetAsync(int? id)` в *Pages/Movies/Details.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b157-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="1b157-139">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="1b157-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="1b157-140">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="1b157-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="1b157-141">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="1b157-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="1b157-142">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="1b157-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="1b157-143">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="1b157-143">Review concurrency exception handling</span></span>

<span data-ttu-id="1b157-144">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b157-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="1b157-145">Предыдущий код обнаруживает исключения параллелизма, когда один клиент удаляет фильм, а другой вносит изменения в фильм.</span><span class="sxs-lookup"><span data-stu-id="1b157-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="1b157-146">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1b157-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="1b157-147">Задайте точку останова в `catch (DbUpdateConcurrencyException)`.</span><span class="sxs-lookup"><span data-stu-id="1b157-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="1b157-148">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="1b157-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="1b157-149">В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.</span><span class="sxs-lookup"><span data-stu-id="1b157-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="1b157-150">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="1b157-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="1b157-151">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="1b157-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="1b157-152">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="1b157-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="1b157-153">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="1b157-153">Posting and binding review</span></span>

<span data-ttu-id="1b157-154">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b157-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="1b157-155">При выполнении HTTP-запроса GET к странице Movies/Edit, например `https://localhost:5001/Movies/Edit/3`, происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1b157-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="1b157-156">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="1b157-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="1b157-157">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1b157-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="1b157-158">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели `@model RazorPagesMovie.Pages.Movies.EditModel`, которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="1b157-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="1b157-159">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="1b157-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="1b157-160">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1b157-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="1b157-161">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1b157-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="1b157-162">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1b157-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="1b157-163">При наличии ошибок в состоянии модели, например `ReleaseDate` невозможно преобразовать в дату, форма отображается повторно с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="1b157-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="1b157-164">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1b157-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="1b157-165">Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично.</span><span class="sxs-lookup"><span data-stu-id="1b157-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="1b157-166">Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="1b157-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b157-167">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1b157-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1b157-168">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="1b157-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b157-169">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="1b157-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="1b157-170">Вместо **ReleaseDate** должно стоять **Дата выпуска**, то есть два слова.</span><span class="sxs-lookup"><span data-stu-id="1b157-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="1b157-172">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="1b157-172">Update the generated code</span></span>

<span data-ttu-id="1b157-173">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="1b157-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="1b157-174">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1b157-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="1b157-175">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="1b157-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="1b157-176">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="1b157-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="1b157-177">Атрибут [Display](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) определяет отображаемое имя поля, в этом случае "Дата выпуска" вместо ReleaseDate.</span><span class="sxs-lookup"><span data-stu-id="1b157-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="1b157-178">Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) задает тип данных (`Date`), поэтому сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="1b157-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="1b157-179">Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit**, чтобы увидеть конечный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="1b157-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="1b157-181">Ссылки **Edit**, **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1b157-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="1b157-182">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="1b157-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="1b157-183">В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе Razor Page (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="1b157-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="1b157-184">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="1b157-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="1b157-185">Для проверки созданной разметки используйте в браузере параметр **Просмотреть исходный код**.</span><span class="sxs-lookup"><span data-stu-id="1b157-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="1b157-186">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="1b157-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="1b157-187">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса.</span><span class="sxs-lookup"><span data-stu-id="1b157-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="1b157-188">Например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="1b157-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="1b157-189">Обновите страницы Razor Edit, Details и Delete так, чтобы использовался шаблон маршрута "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="1b157-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="1b157-190">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="1b157-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="1b157-191">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="1b157-191">Run the app and then view source.</span></span> <span data-ttu-id="1b157-192">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="1b157-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="1b157-193">Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="1b157-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1b157-194">Например, `https://localhost:5001/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="1b157-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="1b157-195">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="1b157-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1b157-196">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="1b157-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="1b157-197">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1b157-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="1b157-198">Установите точку останова`public async Task<IActionResult> OnGetAsync(int? id)` в *Pages/Movies/Details.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b157-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="1b157-199">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="1b157-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="1b157-200">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="1b157-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="1b157-201">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="1b157-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="1b157-202">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="1b157-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="1b157-203">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="1b157-203">Review concurrency exception handling</span></span>

<span data-ttu-id="1b157-204">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b157-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="1b157-205">Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.</span><span class="sxs-lookup"><span data-stu-id="1b157-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="1b157-206">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1b157-206">To test the `catch` block:</span></span>

* <span data-ttu-id="1b157-207">Задайте точку останова в `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="1b157-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="1b157-208">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="1b157-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="1b157-209">В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.</span><span class="sxs-lookup"><span data-stu-id="1b157-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="1b157-210">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="1b157-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="1b157-211">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="1b157-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="1b157-212">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="1b157-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="1b157-213">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="1b157-213">Posting and binding review</span></span>

<span data-ttu-id="1b157-214">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b157-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="1b157-215">При выполнении HTTP-запроса GET к странице Movies/Edit, например `https://localhost:5001/Movies/Edit/3`, происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1b157-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="1b157-216">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="1b157-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="1b157-217">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1b157-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="1b157-218">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели `@model RazorPagesMovie.Pages.Movies.EditModel`, которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="1b157-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="1b157-219">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="1b157-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="1b157-220">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1b157-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="1b157-221">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1b157-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="1b157-222">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1b157-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="1b157-223">При наличии ошибок в состоянии модели, например `ReleaseDate` невозможно преобразовать в дату, форма отображается с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="1b157-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="1b157-224">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1b157-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="1b157-225">Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично.</span><span class="sxs-lookup"><span data-stu-id="1b157-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="1b157-226">Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="1b157-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="1b157-227">В следующем учебнике будет добавлена функция поиска.</span><span class="sxs-lookup"><span data-stu-id="1b157-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b157-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1b157-228">Additional resources</span></span>

* [<span data-ttu-id="1b157-229">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="1b157-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="1b157-230">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="1b157-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
