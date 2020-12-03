---
title: Часть 6. Добавление поиска
author: rick-anderson
description: Часть 6 серии руководств по Razor Pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 3b95fe117895555ebcd44f971e7bb9d1173e1697
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419984"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="4d2cb-103">Часть 6. Добавление поиска в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d2cb-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="4d2cb-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="4d2cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4d2cb-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="4d2cb-106">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d2cb-107">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="4d2cb-108">Добавьте следующие выделенные инструкцию using и свойства в *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="4d2cb-109">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-109">In the previous code:</span></span>

* <span data-ttu-id="4d2cb-110">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="4d2cb-111">`SearchString` также имеет атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="4d2cb-112">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="4d2cb-113">`[BindProperty(SupportsGet = true)]` требуется для привязки в запросах HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="4d2cb-114">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="4d2cb-115">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="4d2cb-116">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="4d2cb-117">`MovieGenre`: содержит конкретный жанр, выбранный пользователем.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="4d2cb-118">Например, "Боевик".</span><span class="sxs-lookup"><span data-stu-id="4d2cb-118">For example, "Western".</span></span>
* <span data-ttu-id="4d2cb-119">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4d2cb-120">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="4d2cb-121">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="4d2cb-122">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-122">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="4d2cb-123">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="4d2cb-124">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4d2cb-125">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="4d2cb-126">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains`или `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="4d2cb-127">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="4d2cb-128">Вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="4d2cb-129">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4d2cb-130">Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="4d2cb-131">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="4d2cb-132">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="4d2cb-133">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="4d2cb-134">Перейдите на страницу Movies и добавьте строку запроса, например `?searchString=Ghost`, к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="4d2cb-135">Например, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="4d2cb-136">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-136">The filtered movies are displayed.</span></span>

![Экран "Представление Index"](search/_static/ghost.png)

<span data-ttu-id="4d2cb-138">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="4d2cb-139">Например, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="4d2cb-140">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="4d2cb-141">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Экран "Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2"](search/_static/g2.png)

<span data-ttu-id="4d2cb-143">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="4d2cb-144">Привязка модели: \**_без_* _ учета регистра.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-144">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="4d2cb-145">Однако пользователи не могут изменять URL-адрес для поиска фильма.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="4d2cb-146">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="4d2cb-147">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="4d2cb-148">Откройте файл _Pages/Movies/Index.cshtml\* и добавьте разметку, выделенную в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="4d2cb-149">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="4d2cb-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="4d2cb-150">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="4d2cb-151">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* через строку запроса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="4d2cb-152">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="4d2cb-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="4d2cb-153">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-153">Save the changes and test the filter.</span></span>

![Экран "Представление Index со словом ghost в текстовом поле фильтра по названию"](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="4d2cb-155">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="4d2cb-155">Search by genre</span></span>

<span data-ttu-id="4d2cb-156">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="4d2cb-157">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="4d2cb-158">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="4d2cb-159">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="4d2cb-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="4d2cb-160">Обновите *Index.cshtml* [элемент `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form), как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="4d2cb-161">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d2cb-162">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4d2cb-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d2cb-163">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="4d2cb-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d2cb-164">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4d2cb-165">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="4d2cb-166">Добавьте следующие выделенные свойства в файл *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="4d2cb-167">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="4d2cb-168">`SearchString` также имеет атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="4d2cb-169">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="4d2cb-170">`[BindProperty(SupportsGet = true)]` требуется для привязки в запросах HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="4d2cb-171">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="4d2cb-172">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="4d2cb-173">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="4d2cb-174">`MovieGenre`: содержит конкретный жанр, выбранный пользователем.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="4d2cb-175">Например, "Боевик".</span><span class="sxs-lookup"><span data-stu-id="4d2cb-175">For example, "Western".</span></span>
* <span data-ttu-id="4d2cb-176">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4d2cb-177">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="4d2cb-178">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="4d2cb-179">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="4d2cb-180">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="4d2cb-181">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4d2cb-182">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="4d2cb-183">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains` или `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="4d2cb-184">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="4d2cb-185">Вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="4d2cb-186">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="4d2cb-187">**Примечание.** Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="4d2cb-188">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="4d2cb-189">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="4d2cb-190">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="4d2cb-191">Перейдите на страницу Movies и добавьте строку запроса, например `?searchString=Ghost`, к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="4d2cb-192">Например, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="4d2cb-193">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-193">The filtered movies are displayed.</span></span>

![Экран "Представление Index"](search/_static/ghost.png)

<span data-ttu-id="4d2cb-195">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="4d2cb-196">Например, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="4d2cb-197">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="4d2cb-198">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Экран "Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2"](search/_static/g2.png)

<span data-ttu-id="4d2cb-200">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="4d2cb-201">Привязка модели: \**_без_* _ учета регистра.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="4d2cb-202">Однако пользователи не могут изменять URL-адрес для поиска фильма.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="4d2cb-203">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="4d2cb-204">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="4d2cb-205">Откройте файл _Pages/Movies/Index.cshtml\* и добавьте разметку `<form>`, выделенную в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="4d2cb-206">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="4d2cb-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="4d2cb-207">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d2cb-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="4d2cb-208">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* через строку запроса.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="4d2cb-209">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="4d2cb-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="4d2cb-210">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-210">Save the changes and test the filter.</span></span>

![Экран "Представление Index со словом ghost в текстовом поле фильтра по названию"](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="4d2cb-212">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="4d2cb-212">Search by genre</span></span>

<span data-ttu-id="4d2cb-213">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="4d2cb-214">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="4d2cb-215">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="4d2cb-216">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="4d2cb-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="4d2cb-217">Обновите *Index.cshtml* [элемент `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form), как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="4d2cb-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="4d2cb-218">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="4d2cb-219">В приведенном выше коде используется [вспомогательные функции тега Select](xref:mvc/views/working-with-forms#the-select-tag-helper) и Option.</span><span class="sxs-lookup"><span data-stu-id="4d2cb-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d2cb-220">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4d2cb-220">Additional resources</span></span>

* [<span data-ttu-id="4d2cb-221">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="4d2cb-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="4d2cb-222">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="4d2cb-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
