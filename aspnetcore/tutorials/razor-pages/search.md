---
title: Добавление поиска на страницы Razor ASP.NET Core
author: rick-anderson
description: Инструкции по добавлению поиска на страницы Razor ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: baa5e1cb2098a60155a4196f0e602feeff04f102
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775002"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="4e716-103">Добавление поиска на страницы Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e716-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="4e716-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="4e716-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="4e716-105">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="4e716-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="4e716-106">Добавьте следующие выделенные свойства в файл *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e716-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="4e716-107">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="4e716-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="4e716-108">`SearchString` также имеет атрибут [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute).</span><span class="sxs-lookup"><span data-stu-id="4e716-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="4e716-109">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="4e716-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="4e716-110">`(SupportsGet = true)` является обязательным для привязки в запросах GET.</span><span class="sxs-lookup"><span data-stu-id="4e716-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="4e716-111">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="4e716-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="4e716-112">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="4e716-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="4e716-113">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="4e716-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="4e716-114">`MovieGenre`: содержит конкретный жанр, выбранный пользователем, например "Western" (Вестерн).</span><span class="sxs-lookup"><span data-stu-id="4e716-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="4e716-115">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="4e716-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4e716-116">Обновите метод `OnGetAsync` страницы Index, добавив следующий код:</span><span class="sxs-lookup"><span data-stu-id="4e716-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="4e716-117">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="4e716-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="4e716-118">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="4e716-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="4e716-119">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="4e716-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="4e716-120">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4e716-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4e716-121">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains` (используется в предшествующем коде).</span><span class="sxs-lookup"><span data-stu-id="4e716-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="4e716-122">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода (например, `Where`, `Contains` или `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="4e716-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="4e716-123">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="4e716-124">Это означает, что вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="4e716-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="4e716-125">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="4e716-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4e716-126">Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="4e716-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="4e716-127">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="4e716-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="4e716-128">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="4e716-129">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="4e716-130">Перейдите на страницу Movies и добавьте строку запроса, такую как `?searchString=Ghost`, к URL-адресу (например, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="4e716-131">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="4e716-131">The filtered movies are displayed.</span></span>

![Представление Index](search/_static/ghost.png)

<span data-ttu-id="4e716-133">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса (например, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="4e716-134">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="4e716-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="4e716-135">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="4e716-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="4e716-137">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="4e716-138">Привязка модели не учитывает регистр символов.</span><span class="sxs-lookup"><span data-stu-id="4e716-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="4e716-139">Тем не менее пользователи вряд ли будут изменять URL-адрес для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4e716-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="4e716-140">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4e716-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="4e716-141">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="4e716-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="4e716-142">Откройте файл *Pages/Movies/Index.cshtml* и добавьте разметку `<form>`, которая выделена в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="4e716-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="4e716-143">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="4e716-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="4e716-144">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4e716-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="4e716-145">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="4e716-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="4e716-146">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="4e716-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="4e716-147">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="4e716-147">Save the changes and test the filter.</span></span>

![Представление Index со словом ghost в текстовом поле фильтра по названию](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="4e716-149">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="4e716-149">Search by genre</span></span>

<span data-ttu-id="4e716-150">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4e716-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="4e716-151">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="4e716-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="4e716-152">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="4e716-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="4e716-153">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="4e716-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="4e716-154">Обновите файл *Index.cshtml* следующим образом:</span><span class="sxs-lookup"><span data-stu-id="4e716-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="4e716-155">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="4e716-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e716-156">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4e716-156">Additional resources</span></span>

* [<span data-ttu-id="4e716-157">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="4e716-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="4e716-158">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="4e716-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="4e716-159">В следующих разделах добавляется поиск фильмов по *жанру* или *имени*.</span><span class="sxs-lookup"><span data-stu-id="4e716-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="4e716-160">Добавьте следующие выделенные свойства в файл *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e716-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="4e716-161">`SearchString`: содержит текст, который пользователи вводят в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="4e716-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="4e716-162">`SearchString` также имеет атрибут [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute).</span><span class="sxs-lookup"><span data-stu-id="4e716-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="4e716-163">`[BindProperty]` связывает значения из формы и строки запроса с тем же именем, что и у свойства.</span><span class="sxs-lookup"><span data-stu-id="4e716-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="4e716-164">`(SupportsGet = true)` является обязательным для привязки в запросах GET.</span><span class="sxs-lookup"><span data-stu-id="4e716-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="4e716-165">`Genres`: содержит список жанров.</span><span class="sxs-lookup"><span data-stu-id="4e716-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="4e716-166">`Genres` дает пользователю возможность выбрать жанр в списке.</span><span class="sxs-lookup"><span data-stu-id="4e716-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="4e716-167">Для `SelectList` требуется `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="4e716-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="4e716-168">`MovieGenre`: содержит конкретный жанр, выбранный пользователем, например "Western" (Вестерн).</span><span class="sxs-lookup"><span data-stu-id="4e716-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="4e716-169">`Genres` и `MovieGenre` рассматриваются позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="4e716-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4e716-170">Обновите метод `OnGetAsync` страницы Index, добавив следующий код:</span><span class="sxs-lookup"><span data-stu-id="4e716-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="4e716-171">В первой строке метода `OnGetAsync` создается запрос [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) для выбора фильмов:</span><span class="sxs-lookup"><span data-stu-id="4e716-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="4e716-172">Этот запрос *только* определяется в этой точке и **не** выполняется для базы данных.</span><span class="sxs-lookup"><span data-stu-id="4e716-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="4e716-173">Если свойство `SearchString` не равно NULL и не пусто, запрос фильмов изменяется для фильтрации по строке поиска:</span><span class="sxs-lookup"><span data-stu-id="4e716-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="4e716-174">Код `s => s.Title.Contains()` представляет собой [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="4e716-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4e716-175">Лямбда-выражения используются в запросах [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) на основе методов в качестве аргументов стандартных методов операторов запроса, таких как метод [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) или `Contains` (используется в предшествующем коде).</span><span class="sxs-lookup"><span data-stu-id="4e716-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="4e716-176">Запросы LINQ не выполняются, если они определяются или изменяются путем вызова метода (например, `Where`, `Contains` или `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="4e716-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="4e716-177">Вместо этого выполнение запроса откладывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="4e716-178">Это означает, что вычисление выражения откладывается до тех пор, пока не будет выполнена итерация его реализованного значения или не будет вызван метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="4e716-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="4e716-179">Дополнительные сведения см. в разделе [Выполнение запроса](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="4e716-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="4e716-180">**Примечание.** Метод [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) выполняется в базе данных, а не в коде C#.</span><span class="sxs-lookup"><span data-stu-id="4e716-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="4e716-181">Регистр символов запроса учитывается в зависимости от параметров базы данных и сортировки.</span><span class="sxs-lookup"><span data-stu-id="4e716-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="4e716-182">В SQL Server метод `Contains` сопоставляется с [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), в котором регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="4e716-183">В SQLite при параметрах сортировки по умолчанию регистр символов учитывается.</span><span class="sxs-lookup"><span data-stu-id="4e716-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="4e716-184">Перейдите на страницу Movies и добавьте строку запроса, такую как `?searchString=Ghost`, к URL-адресу (например, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="4e716-185">Отображаются отфильтрованные фильмы.</span><span class="sxs-lookup"><span data-stu-id="4e716-185">The filtered movies are displayed.</span></span>

![Представление Index](search/_static/ghost.png)

<span data-ttu-id="4e716-187">Если на страницу Index добавлен следующий шаблон маршрута, строку поиска можно передать в виде сегмента URL-адреса (например, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="4e716-188">Предыдущее ограничение маршрута разрешало поиск названия в виде данных маршрута (сегмент URL-адреса) вместо значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="4e716-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="4e716-189">Символ `?` в `"{searchString?}"` означает, что этот параметр является необязательным.</span><span class="sxs-lookup"><span data-stu-id="4e716-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Представление Index, в URL-адрес которого добавлено слово ghost, возвращает два фильма: Ghostbusters и Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="4e716-191">Среда выполнения ASP.NET Core использует [привязку модели](xref:mvc/models/model-binding), чтобы присвоить значение свойства `SearchString` по строке запроса (`?searchString=Ghost`) или данным маршрута (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="4e716-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="4e716-192">Привязка модели не учитывает регистр символов.</span><span class="sxs-lookup"><span data-stu-id="4e716-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="4e716-193">Тем не менее пользователи вряд ли будут изменять URL-адрес для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4e716-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="4e716-194">На этом шаге добавляется пользовательский интерфейс для поиска фильмов.</span><span class="sxs-lookup"><span data-stu-id="4e716-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="4e716-195">Если было добавлено ограничение маршрута `"{searchString?}"`, удалите его.</span><span class="sxs-lookup"><span data-stu-id="4e716-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="4e716-196">Откройте файл *Pages/Movies/Index.cshtml* и добавьте разметку `<form>`, которая выделена в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="4e716-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="4e716-197">Тег HTML `<form>` использует следующие [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="4e716-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="4e716-198">[вспомогательная функция тега форм](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4e716-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="4e716-199">При отправке формы строка фильтра отправляется на страницу *Pages/Movies/Index* в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="4e716-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="4e716-200">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="4e716-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="4e716-201">Сохраните изменения и проверьте работу фильтра.</span><span class="sxs-lookup"><span data-stu-id="4e716-201">Save the changes and test the filter.</span></span>

![Представление Index со словом ghost в текстовом поле фильтра по названию](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="4e716-203">Поиск по жанру</span><span class="sxs-lookup"><span data-stu-id="4e716-203">Search by genre</span></span>

<span data-ttu-id="4e716-204">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="4e716-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="4e716-205">Следующий код определяет запрос LINQ, который извлекает все жанры из базы данных.</span><span class="sxs-lookup"><span data-stu-id="4e716-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="4e716-206">Список жанров `SelectList` создается путем проецирования отдельных жанров.</span><span class="sxs-lookup"><span data-stu-id="4e716-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="4e716-207">Добавление поиска по жанру на страницу Razor</span><span class="sxs-lookup"><span data-stu-id="4e716-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="4e716-208">Обновите файл *Index.cshtml* следующим образом:</span><span class="sxs-lookup"><span data-stu-id="4e716-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="4e716-209">Проверьте работу приложения, выполнив поиск по жанру, по названию фильма и по обоим этим параметрам.</span><span class="sxs-lookup"><span data-stu-id="4e716-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="4e716-210">В приведенном выше коде используется [вспомогательные функции тега Select](xref:mvc/views/working-with-forms#the-select-tag-helper) и Option.</span><span class="sxs-lookup"><span data-stu-id="4e716-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e716-211">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4e716-211">Additional resources</span></span>

* [<span data-ttu-id="4e716-212">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="4e716-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="4e716-213">[Предыдущая статья. Обновление страниц](xref:tutorials/razor-pages/da1)
> [Следующая статья. Добавление нового поля](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="4e716-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
