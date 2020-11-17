---
title: Часть 5. Обновление созданных страниц
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages.
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360612"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="1552b-103">Часть 5. Изменение созданных страниц в приложении ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1552b-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="1552b-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1552b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1552b-105">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="1552b-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="1552b-106">Вместо **ReleaseDate** должно стоять **Дата выпуска**, то есть два слова.</span><span class="sxs-lookup"><span data-stu-id="1552b-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="1552b-108">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="1552b-108">Update the generated code</span></span>

<span data-ttu-id="1552b-109">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="1552b-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="1552b-110">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="1552b-110">In the previous code:</span></span>

* <span data-ttu-id="1552b-111">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1552b-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="1552b-112">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="1552b-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="1552b-113">Атрибут [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) указывает на отображаемое имя поля.</span><span class="sxs-lookup"><span data-stu-id="1552b-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="1552b-114">В приведенном выше коде ReleaseDate заменен на "Дата выпуска".</span><span class="sxs-lookup"><span data-stu-id="1552b-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="1552b-115">Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="1552b-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="1552b-116">Сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="1552b-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="1552b-117">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="1552b-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="1552b-118">Перейдите к *Pages/Movies* и наведите указатель мыши на ссылку **Edit** (Изменение), чтобы просмотреть целевой URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="1552b-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="1552b-120">Ссылки **Edit** (Изменение), **Details** (Сведения) и **Delete (Удаление)** создаются [Вспомогательной функцией привязки тегов](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1552b-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="1552b-121">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="1552b-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="1552b-122">В приведенном выше коде [Вспомогательная функция привязки тегов](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) динамически создает значение атрибута HTML `href` на основе Razor Page (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="1552b-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="1552b-123">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="1552b-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="1552b-124">Для проверки созданной разметки используйте в браузере параметр **Просмотреть исходный код**.</span><span class="sxs-lookup"><span data-stu-id="1552b-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="1552b-125">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="1552b-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="1552b-126">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса.</span><span class="sxs-lookup"><span data-stu-id="1552b-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="1552b-127">Например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="1552b-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="1552b-128">Добавление шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="1552b-128">Add route template</span></span>

<span data-ttu-id="1552b-129">Обновите страницы Edit (Изменение), Details (Сведения) и Delete(Удаление) так, чтобы в Razor Pages использовался шаблон маршрута `{id:int}`.</span><span class="sxs-lookup"><span data-stu-id="1552b-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="1552b-130">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="1552b-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="1552b-131">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="1552b-131">Run the app and then view source.</span></span>

<span data-ttu-id="1552b-132">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="1552b-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="1552b-133">Запрос к странице с шаблоном маршрута `{id:int}`, который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="1552b-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1552b-134">Например, `https://localhost:5001/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="1552b-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="1552b-135">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="1552b-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1552b-136">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="1552b-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="1552b-137">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1552b-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="1552b-138">Установите точку останова`public async Task<IActionResult> OnGetAsync(int? id)` в *Pages/Movies/Details.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1552b-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="1552b-139">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="1552b-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="1552b-140">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="1552b-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="1552b-141">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="1552b-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="1552b-142">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="1552b-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="1552b-143">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="1552b-143">Review concurrency exception handling</span></span>

<span data-ttu-id="1552b-144">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1552b-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="1552b-145">Предыдущий код обнаруживает исключения параллелизма, когда один клиент удаляет фильм, а другой вносит изменения в фильм.</span><span class="sxs-lookup"><span data-stu-id="1552b-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="1552b-146">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1552b-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="1552b-147">Задайте точку останова в `catch (DbUpdateConcurrencyException)`.</span><span class="sxs-lookup"><span data-stu-id="1552b-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="1552b-148">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="1552b-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="1552b-149">В другом окне браузера выберите ссылку **Delete (Удаление)** для того же фильма, а затем удалите фильм.</span><span class="sxs-lookup"><span data-stu-id="1552b-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="1552b-150">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="1552b-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="1552b-151">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="1552b-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="1552b-152">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="1552b-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="1552b-153">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="1552b-153">Posting and binding review</span></span>

<span data-ttu-id="1552b-154">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1552b-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="1552b-155">При выполнении HTTP-запроса GET к странице Movies/Edit, например `https://localhost:5001/Movies/Edit/3`, происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1552b-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="1552b-156">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="1552b-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="1552b-157">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1552b-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="1552b-158">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели `@model RazorPagesMovie.Pages.Movies.EditModel`, которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="1552b-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="1552b-159">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="1552b-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="1552b-160">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1552b-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="1552b-161">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1552b-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="1552b-162">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1552b-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="1552b-163">При наличии ошибок в состоянии модели, например `ReleaseDate` невозможно преобразовать в дату, форма отображается повторно с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="1552b-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="1552b-164">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1552b-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="1552b-165">Методы HTTP GET на страницах Index, Create и Delete Razor соответствуют аналогичному шаблону.</span><span class="sxs-lookup"><span data-stu-id="1552b-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="1552b-166">Метод HTTP POST `OnPostAsync` в CreateRazor Page соответствует аналогичному шаблону метода `OnPostAsync` в Edit Razor Page.</span><span class="sxs-lookup"><span data-stu-id="1552b-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1552b-167">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1552b-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1552b-168">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="1552b-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1552b-169">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="1552b-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="1552b-170">Вместо **ReleaseDate** должно стоять **Дата выпуска**, то есть два слова.</span><span class="sxs-lookup"><span data-stu-id="1552b-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="1552b-172">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="1552b-172">Update the generated code</span></span>

<span data-ttu-id="1552b-173">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="1552b-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="1552b-174">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1552b-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="1552b-175">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="1552b-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="1552b-176">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="1552b-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="1552b-177">Атрибут [Display](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) определяет отображаемое имя поля, в этом случае "Дата выпуска" вместо ReleaseDate.</span><span class="sxs-lookup"><span data-stu-id="1552b-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="1552b-178">Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) задает тип данных (`Date`), поэтому сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="1552b-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="1552b-179">Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit**, чтобы увидеть конечный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="1552b-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="1552b-181">Ссылки **Edit** (Изменение), **Details** (Сведения) и **Delete (Удаление)** создаются [Вспомогательной функцией привязки тегов](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1552b-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="1552b-182">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="1552b-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="1552b-183">В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе Razor Page (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="1552b-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="1552b-184">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="1552b-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="1552b-185">Для проверки созданной разметки используйте в браузере параметр **Просмотреть исходный код**.</span><span class="sxs-lookup"><span data-stu-id="1552b-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="1552b-186">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="1552b-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="1552b-187">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса.</span><span class="sxs-lookup"><span data-stu-id="1552b-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="1552b-188">Например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="1552b-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="1552b-189">Обновите страницы Edit (Изменение), Details (Сведения) и Delete(Удаление) так, чтобы в Razor Pages использовался шаблон маршрута "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="1552b-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="1552b-190">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="1552b-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="1552b-191">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="1552b-191">Run the app and then view source.</span></span> <span data-ttu-id="1552b-192">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="1552b-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="1552b-193">Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="1552b-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1552b-194">Например, `https://localhost:5001/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="1552b-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="1552b-195">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="1552b-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1552b-196">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="1552b-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="1552b-197">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1552b-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="1552b-198">Установите точку останова`public async Task<IActionResult> OnGetAsync(int? id)` в *Pages/Movies/Details.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1552b-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="1552b-199">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="1552b-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="1552b-200">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="1552b-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="1552b-201">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="1552b-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="1552b-202">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="1552b-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="1552b-203">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="1552b-203">Review concurrency exception handling</span></span>

<span data-ttu-id="1552b-204">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="1552b-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="1552b-205">Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.</span><span class="sxs-lookup"><span data-stu-id="1552b-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="1552b-206">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1552b-206">To test the `catch` block:</span></span>

* <span data-ttu-id="1552b-207">Задайте точку останова в `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="1552b-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="1552b-208">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="1552b-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="1552b-209">В другом окне браузера выберите ссылку **Delete (Удаление)** для того же фильма, а затем удалите фильм.</span><span class="sxs-lookup"><span data-stu-id="1552b-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="1552b-210">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="1552b-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="1552b-211">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="1552b-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="1552b-212">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="1552b-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="1552b-213">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="1552b-213">Posting and binding review</span></span>

<span data-ttu-id="1552b-214">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1552b-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="1552b-215">При выполнении HTTP-запроса GET к странице Movies/Edit, например `https://localhost:5001/Movies/Edit/3`, происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1552b-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="1552b-216">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="1552b-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="1552b-217">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1552b-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="1552b-218">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели `@model RazorPagesMovie.Pages.Movies.EditModel`, которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="1552b-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="1552b-219">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="1552b-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="1552b-220">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="1552b-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="1552b-221">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1552b-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="1552b-222">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1552b-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="1552b-223">При наличии ошибок в состоянии модели, например `ReleaseDate` невозможно преобразовать в дату, форма отображается с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="1552b-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="1552b-224">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="1552b-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="1552b-225">Методы HTTP GET на страницах Index, Create и Delete Razor соответствуют аналогичному шаблону.</span><span class="sxs-lookup"><span data-stu-id="1552b-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="1552b-226">Метод HTTP POST `OnPostAsync` в CreateRazor Page соответствует аналогичному шаблону метода `OnPostAsync` в Edit Razor Page.</span><span class="sxs-lookup"><span data-stu-id="1552b-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="1552b-227">В следующем учебнике будет добавлена функция поиска.</span><span class="sxs-lookup"><span data-stu-id="1552b-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1552b-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1552b-228">Additional resources</span></span>

* [<span data-ttu-id="1552b-229">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="1552b-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="1552b-230">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="1552b-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
