---
title: Часть 5. Изменение созданных страниц в приложении ASP.NET Core
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 144c4e7f07986c0a10089d74991fe3b44c013894
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652858"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="84d65-103">Часть 5. Изменение созданных страниц в приложении ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84d65-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="84d65-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="84d65-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84d65-105">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="84d65-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="84d65-106">Вместо **ReleaseDate** должно стоять **Дата выпуска** (два слова).</span><span class="sxs-lookup"><span data-stu-id="84d65-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="84d65-108">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="84d65-108">Update the generated code</span></span>

<span data-ttu-id="84d65-109">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="84d65-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="84d65-110">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="84d65-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="84d65-111">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="84d65-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="84d65-112">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="84d65-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="84d65-113">Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="84d65-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="84d65-114">Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="84d65-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="84d65-115">Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit**, чтобы увидеть конечный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="84d65-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="84d65-117">Ссылки **Edit**, **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84d65-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="84d65-118">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="84d65-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="84d65-119">В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе страницы Razor (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="84d65-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="84d65-120">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="84d65-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="84d65-121">Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере.</span><span class="sxs-lookup"><span data-stu-id="84d65-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="84d65-122">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="84d65-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="84d65-123">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса (например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="84d65-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="84d65-124">Добавление шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="84d65-124">Add route template</span></span>

<span data-ttu-id="84d65-125">Обновите страницы Razor Edit, Details и Delete так, чтобы использовался шаблон маршрута "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="84d65-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="84d65-126">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="84d65-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="84d65-127">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="84d65-127">Run the app and then view source.</span></span> <span data-ttu-id="84d65-128">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="84d65-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="84d65-129">Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="84d65-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="84d65-130">Например, `http://localhost:5000/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="84d65-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="84d65-131">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="84d65-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="84d65-132">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="84d65-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="84d65-133">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="84d65-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="84d65-134">Установите точку останова `public async Task<IActionResult> OnGetAsync(int? id)` (в *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="84d65-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="84d65-135">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="84d65-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="84d65-136">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="84d65-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="84d65-137">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="84d65-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="84d65-138">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="84d65-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="84d65-139">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="84d65-139">Review concurrency exception handling</span></span>

<span data-ttu-id="84d65-140">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="84d65-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="84d65-141">Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="84d65-142">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="84d65-142">To test the `catch` block:</span></span>

* <span data-ttu-id="84d65-143">Задайте точку останова в `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="84d65-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="84d65-144">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="84d65-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="84d65-145">В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.</span><span class="sxs-lookup"><span data-stu-id="84d65-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="84d65-146">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="84d65-147">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="84d65-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="84d65-148">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="84d65-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="84d65-149">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="84d65-149">Posting and binding review</span></span>

<span data-ttu-id="84d65-150">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="84d65-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="84d65-151">При выполнении HTTP-запроса GET к странице Movies/Edit (например, `http://localhost:5000/Movies/Edit/2`) происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="84d65-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="84d65-152">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="84d65-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="84d65-153">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84d65-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="84d65-154">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели (`@model RazorPagesMovie.Pages.Movies.EditModel`), которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="84d65-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="84d65-155">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="84d65-156">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="84d65-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="84d65-157">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="84d65-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="84d65-158">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="84d65-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="84d65-159">При наличии ошибок в состоянии модели (например, `ReleaseDate` невозможно преобразовать в дату) форма отображается повторно с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="84d65-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="84d65-160">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="84d65-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="84d65-161">Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично.</span><span class="sxs-lookup"><span data-stu-id="84d65-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="84d65-162">Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="84d65-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84d65-163">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="84d65-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="84d65-164">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="84d65-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84d65-165">Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала.</span><span class="sxs-lookup"><span data-stu-id="84d65-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="84d65-166">Вместо **ReleaseDate** должно стоять **Дата выпуска** (два слова).</span><span class="sxs-lookup"><span data-stu-id="84d65-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="84d65-168">Обновление созданного кода</span><span class="sxs-lookup"><span data-stu-id="84d65-168">Update the generated code</span></span>

<span data-ttu-id="84d65-169">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:</span><span class="sxs-lookup"><span data-stu-id="84d65-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="84d65-170">Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="84d65-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="84d65-171">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="84d65-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="84d65-172">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="84d65-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="84d65-173">Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="84d65-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="84d65-174">Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="84d65-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="84d65-175">Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit**, чтобы увидеть конечный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="84d65-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="84d65-177">Ссылки **Edit**, **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84d65-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="84d65-178">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="84d65-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="84d65-179">В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе страницы Razor (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="84d65-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="84d65-180">Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="84d65-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="84d65-181">Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере.</span><span class="sxs-lookup"><span data-stu-id="84d65-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="84d65-182">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="84d65-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="84d65-183">В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса (например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="84d65-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="84d65-184">Обновите страницы Razor Edit, Details и Delete так, чтобы использовался шаблон маршрута "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="84d65-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="84d65-185">Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="84d65-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="84d65-186">Запустите приложение и просмотрите исходный код.</span><span class="sxs-lookup"><span data-stu-id="84d65-186">Run the app and then view source.</span></span> <span data-ttu-id="84d65-187">Созданный код HTML добавляет идентификатор в путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="84d65-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="84d65-188">Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="84d65-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="84d65-189">Например, `http://localhost:5000/Movies/Details` приведет к ошибке 404.</span><span class="sxs-lookup"><span data-stu-id="84d65-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="84d65-190">Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:</span><span class="sxs-lookup"><span data-stu-id="84d65-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="84d65-191">Чтобы проверить поведение `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="84d65-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="84d65-192">Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="84d65-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="84d65-193">Установите точку останова `public async Task<IActionResult> OnGetAsync(int? id)` (в *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="84d65-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="84d65-194">Перейдите к `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="84d65-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="84d65-195">Из-за директивы `@page "{id:int}"` точка останова не достигается.</span><span class="sxs-lookup"><span data-stu-id="84d65-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="84d65-196">Механизм маршрутизации возвращает ошибку HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="84d65-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="84d65-197">При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="84d65-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="84d65-198">Проверка обработки исключений нежесткой блокировки</span><span class="sxs-lookup"><span data-stu-id="84d65-198">Review concurrency exception handling</span></span>

<span data-ttu-id="84d65-199">Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="84d65-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="84d65-200">Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="84d65-201">Чтобы протестировать блок `catch`, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="84d65-201">To test the `catch` block:</span></span>

* <span data-ttu-id="84d65-202">Задайте точку останова в `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="84d65-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="84d65-203">Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="84d65-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="84d65-204">В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.</span><span class="sxs-lookup"><span data-stu-id="84d65-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="84d65-205">В первом окне браузера опубликуйте изменения для фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="84d65-206">Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="84d65-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="84d65-207">Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="84d65-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="84d65-208">Проверка публикации и привязки</span><span class="sxs-lookup"><span data-stu-id="84d65-208">Posting and binding review</span></span>

<span data-ttu-id="84d65-209">Изучите файл *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="84d65-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="84d65-210">При выполнении HTTP-запроса GET к странице Movies/Edit (например, `http://localhost:5000/Movies/Edit/2`) происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="84d65-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="84d65-211">Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.</span><span class="sxs-lookup"><span data-stu-id="84d65-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="84d65-212">Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="84d65-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="84d65-213">Файл *Pages/Movies/Edit.cshtml* содержит директиву модели (`@model RazorPagesMovie.Pages.Movies.EditModel`), которая делает модель фильма доступной на странице.</span><span class="sxs-lookup"><span data-stu-id="84d65-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="84d65-214">Отображается форма Edit со значениями из записи фильма.</span><span class="sxs-lookup"><span data-stu-id="84d65-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="84d65-215">При публикации страницы Movies/Edit происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="84d65-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="84d65-216">Значения формы на странице привязываются к свойству `Movie`.</span><span class="sxs-lookup"><span data-stu-id="84d65-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="84d65-217">Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="84d65-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="84d65-218">При наличии ошибок в состоянии модели (например, `ReleaseDate` невозможно преобразовать в дату) форма отображается с предоставленными значениями.</span><span class="sxs-lookup"><span data-stu-id="84d65-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="84d65-219">Если ошибки модели отсутствуют, данные фильма сохраняются.</span><span class="sxs-lookup"><span data-stu-id="84d65-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="84d65-220">Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично.</span><span class="sxs-lookup"><span data-stu-id="84d65-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="84d65-221">Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="84d65-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="84d65-222">В следующем учебнике будет добавлена функция поиска.</span><span class="sxs-lookup"><span data-stu-id="84d65-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84d65-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="84d65-223">Additional resources</span></span>

* [<span data-ttu-id="84d65-224">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="84d65-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="84d65-225">[Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="84d65-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
