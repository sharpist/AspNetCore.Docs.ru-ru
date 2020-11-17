---
title: Часть 6. Добавление поиска
author: rick-anderson
description: Часть 6 серии руководств по Razor Pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 00c1be2704d92c7d4f868e6eaa346bd8e9901dbf
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360846"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="30d52-103">Часть 6. Добавление поиска в Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30d52-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="30d52-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="30d52-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="30d52-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="30d52-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="30d52-106">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="30d52-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="30d52-107">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="30d52-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="30d52-108">Добавьте следующие выделенные инструкцию using и свойства в *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="30d52-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="30d52-109">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="30d52-109">In the previous code:</span></span>

* <span data-ttu-id="30d52-110">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="30d52-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="30d52-111">`SearchString` также имеет атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="30d52-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="30d52-112">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="30d52-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="30d52-113">`[BindProperty(SupportsGet = true)]` требуется для привязки в запросах HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="30d52-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="30d52-114">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="30d52-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="30d52-115">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="30d52-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="30d52-116">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="30d52-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="30d52-117">`MovieGenre`: содержит конкретный жанр, выбранный пользователем.</span><span class="sxs-lookup"><span data-stu-id="30d52-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="30d52-118">Например, "Боевик".</span><span class="sxs-lookup"><span data-stu-id="30d52-118">For example, "Western".</span></span>
* <span data-ttu-id="30d52-119">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="30d52-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="30d52-120">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="30d52-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="30d52-121">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="30d52-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="30d52-122">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="30d52-122">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="30d52-123">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="30d52-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="30d52-124">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="30d52-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="30d52-125">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains`.</span><span class="sxs-lookup"><span data-stu-id="30d52-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="30d52-126">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains`или `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="30d52-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="30d52-127">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="30d52-128">Вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="30d52-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="30d52-129">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="30d52-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="30d52-130">Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="30d52-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="30d52-131">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="30d52-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="30d52-132">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="30d52-133">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="30d52-134">Перейдите на страницу Movies и добавьте строку запроса, например `?searchString=Ghost`, к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="30d52-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="30d52-135">Например, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="30d52-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="30d52-136">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="30d52-136">The filtered movies are displayed.</span></span>

![Экран "Представление Index"](search/_static/ghost.png)

<span data-ttu-id="30d52-138">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="30d52-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="30d52-139">Например, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="30d52-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="30d52-140">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="30d52-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="30d52-141">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="30d52-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Экран "Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2"](search/_static/g2.png)

<span data-ttu-id="30d52-143">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30d52-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="30d52-144">Привязка модели: \**_без_* _ учета регистра.</span><span class="sxs-lookup"><span data-stu-id="30d52-144">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="30d52-145">Однако пользователи не могут изменять URL-адрес для поиска фильма.</span><span class="sxs-lookup"><span data-stu-id="30d52-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="30d52-146">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="30d52-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="30d52-147">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="30d52-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="30d52-148">Откройте файл _Pages/Movies/Index.cshtml\* и добавьте разметку, выделенную в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="30d52-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="30d52-149">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="30d52-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="30d52-150">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="30d52-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="30d52-151">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* через строку запроса.</span><span class="sxs-lookup"><span data-stu-id="30d52-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="30d52-152">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="30d52-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="30d52-153">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="30d52-153">Save the changes and test the filter.</span></span>

![Экран "Представление Index со словом ghost в текстовом поле фильтра по названию"](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="30d52-155">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="30d52-155">Search by genre</span></span>

<span data-ttu-id="30d52-156">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="30d52-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="30d52-157">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="30d52-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="30d52-158">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="30d52-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="30d52-159">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="30d52-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="30d52-160">Обновите *Index.cshtml* [элемент `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form), как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="30d52-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="30d52-161">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="30d52-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30d52-162">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="30d52-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="30d52-163">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="30d52-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="30d52-164">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="30d52-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="30d52-165">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="30d52-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="30d52-166">Добавьте следующие выделенные свойства в файл *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="30d52-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="30d52-167">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="30d52-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="30d52-168">`SearchString` также имеет атрибут [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="30d52-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="30d52-169">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="30d52-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="30d52-170">`[BindProperty(SupportsGet = true)]` требуется для привязки в запросах HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="30d52-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="30d52-171">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="30d52-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="30d52-172">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="30d52-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="30d52-173">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="30d52-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="30d52-174">`MovieGenre`: содержит конкретный жанр, выбранный пользователем.</span><span class="sxs-lookup"><span data-stu-id="30d52-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="30d52-175">Например, "Боевик".</span><span class="sxs-lookup"><span data-stu-id="30d52-175">For example, "Western".</span></span>
* <span data-ttu-id="30d52-176">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="30d52-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="30d52-177">Обновите метод `OnGetAsync` страницы Index, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="30d52-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="30d52-178">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="30d52-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="30d52-179">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="30d52-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="30d52-180">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="30d52-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="30d52-181">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="30d52-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="30d52-182">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains`.</span><span class="sxs-lookup"><span data-stu-id="30d52-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="30d52-183">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода, например `Where`, `Contains` или `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="30d52-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="30d52-184">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="30d52-185">Вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="30d52-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="30d52-186">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="30d52-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="30d52-187">**Примечание.** Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="30d52-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="30d52-188">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="30d52-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="30d52-189">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="30d52-190">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="30d52-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="30d52-191">Перейдите на страницу Movies и добавьте строку запроса, например `?searchString=Ghost`, к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="30d52-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="30d52-192">Например, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="30d52-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="30d52-193">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="30d52-193">The filtered movies are displayed.</span></span>

![Экран "Представление Index"](search/_static/ghost.png)

<span data-ttu-id="30d52-195">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="30d52-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="30d52-196">Например, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="30d52-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="30d52-197">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="30d52-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="30d52-198">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="30d52-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Экран "Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2"](search/_static/g2.png)

<span data-ttu-id="30d52-200">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30d52-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="30d52-201">Привязка модели: \**_без_* _ учета регистра.</span><span class="sxs-lookup"><span data-stu-id="30d52-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="30d52-202">Однако пользователи не могут изменять URL-адрес для поиска фильма.</span><span class="sxs-lookup"><span data-stu-id="30d52-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="30d52-203">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="30d52-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="30d52-204">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="30d52-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="30d52-205">Откройте файл _Pages/Movies/Index.cshtml\* и добавьте разметку `<form>`, выделенную в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="30d52-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="30d52-206">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="30d52-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="30d52-207">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="30d52-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="30d52-208">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* через строку запроса.</span><span class="sxs-lookup"><span data-stu-id="30d52-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="30d52-209">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="30d52-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="30d52-210">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="30d52-210">Save the changes and test the filter.</span></span>

![Экран "Представление Index со словом ghost в текстовом поле фильтра по названию"](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="30d52-212">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="30d52-212">Search by genre</span></span>

<span data-ttu-id="30d52-213">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="30d52-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="30d52-214">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="30d52-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="30d52-215">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="30d52-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="30d52-216">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="30d52-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="30d52-217">Обновите *Index.cshtml* [элемент `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form), как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="30d52-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="30d52-218">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="30d52-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="30d52-219">В приведенном выше коде используется [вспомогательные функции тега Select](xref:mvc/views/working-with-forms#the-select-tag-helper) и Option.</span><span class="sxs-lookup"><span data-stu-id="30d52-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30d52-220">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="30d52-220">Additional resources</span></span>

* [<span data-ttu-id="30d52-221">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="30d52-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="30d52-222">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="30d52-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
