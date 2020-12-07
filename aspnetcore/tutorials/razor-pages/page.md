---
title: Часть 3. Шаблонные Razor Pages
author: rick-anderson
description: Часть 3 серии руководств по Razor Pages.
ms.author: riande
ms.date: 09/25/2020
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 4a5369b9e40de89ac9a1895466e7bdd7afb9d32e
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420036"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="d6baf-103">Часть 3. Razor Pages, созданные путем формирования шаблонов, в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6baf-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="d6baf-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="d6baf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d6baf-105">Этот учебник описывает Razor Pages, созданные путем формирования шаблонов в [предыдущем учебнике](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="d6baf-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d6baf-106">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d6baf-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="d6baf-107">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d6baf-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="d6baf-108">Страницы Create, Delete, Details и Edit</span><span class="sxs-lookup"><span data-stu-id="d6baf-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="d6baf-109">Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="d6baf-110">Razor Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="d6baf-111">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="d6baf-112">Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`:</span><span class="sxs-lookup"><span data-stu-id="d6baf-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d6baf-113">Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="d6baf-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="d6baf-114">Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов.</span><span class="sxs-lookup"><span data-stu-id="d6baf-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="d6baf-115">В Razor Page для инициализации состояния страницы вызывается `OnGetAsync` или `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="d6baf-116">В этом случае `OnGetAsync` возвращает список фильмов для отображения.</span><span class="sxs-lookup"><span data-stu-id="d6baf-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="d6baf-117">Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, оператор return не используется.</span><span class="sxs-lookup"><span data-stu-id="d6baf-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="d6baf-118">Например, страница "Конфиденциальность":</span><span class="sxs-lookup"><span data-stu-id="d6baf-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="d6baf-119">Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return.</span><span class="sxs-lookup"><span data-stu-id="d6baf-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="d6baf-120">Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="d6baf-121">Изучите страницу *Pages/Movies/Index.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="d6baf-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="d6baf-122">Razor может выполнять переход с HTML на C# или на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="d6baf-123">Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="d6baf-124">директиву @page</span><span class="sxs-lookup"><span data-stu-id="d6baf-124">The @page directive</span></span>

<span data-ttu-id="d6baf-125">Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="d6baf-126">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="d6baf-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="d6baf-127">`@page` и `@model` являются примерами перехода на разметку, относящуюся к Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="d6baf-128">Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).</span><span class="sxs-lookup"><span data-stu-id="d6baf-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="d6baf-129">директиву @model</span><span class="sxs-lookup"><span data-stu-id="d6baf-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="d6baf-130">Директива `@model` определяет тип модели, передаваемой на страницу Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="d6baf-131">В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="d6baf-132">Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="d6baf-133">Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:</span><span class="sxs-lookup"><span data-stu-id="d6baf-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="d6baf-134">Вспомогательный метод HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя.</span><span class="sxs-lookup"><span data-stu-id="d6baf-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="d6baf-135">Лямбда-выражение проверяется, а не вычисляется.</span><span class="sxs-lookup"><span data-stu-id="d6baf-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="d6baf-136">Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются.</span><span class="sxs-lookup"><span data-stu-id="d6baf-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="d6baf-137">При вычислении лямбда-выражения, например с помощью `@Html.DisplayFor(modelItem => item.Title)`, вычисляются значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="d6baf-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="d6baf-138">Страница макета</span><span class="sxs-lookup"><span data-stu-id="d6baf-138">The layout page</span></span>

<span data-ttu-id="d6baf-139">Выберите ссылки в меню **RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="d6baf-140">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="d6baf-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="d6baf-141">Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d6baf-142">Откройте и проверьте файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d6baf-143">Шаблоны [макета](xref:mvc/views/layout) позволяют сделать следующее для макета контейнера HTML:</span><span class="sxs-lookup"><span data-stu-id="d6baf-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="d6baf-144">указать его в одном расположении;</span><span class="sxs-lookup"><span data-stu-id="d6baf-144">Specified in one place.</span></span>
* <span data-ttu-id="d6baf-145">применить его на нескольких страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="d6baf-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="d6baf-146">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d6baf-147">`RenderBody` — это заполнитель для отображения всех представлений определенных страниц, *упакованных* в страницу макета.</span><span class="sxs-lookup"><span data-stu-id="d6baf-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d6baf-148">Например, щелкните ссылку **Конфиденциальность**, и представление *Pages/Privacy.cshtml* отобразится в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="d6baf-149">ViewData и макет</span><span class="sxs-lookup"><span data-stu-id="d6baf-149">ViewData and layout</span></span>

<span data-ttu-id="d6baf-150">Рассмотрим следующую разметку из файла *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="d6baf-151">Выделенная выше разметка представляет собой пример перехода Razor на C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="d6baf-152">Символы `{` и `}` ограничивают блок кода C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="d6baf-153">Базовый класс `PageModel` содержит свойство словаря `ViewData`. Оно позволяет передать данные в представление.</span><span class="sxs-lookup"><span data-stu-id="d6baf-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="d6baf-154">Объекты добавляются в словарь `ViewData` с помощью шаблона \***key value** _.</span><span class="sxs-lookup"><span data-stu-id="d6baf-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="d6baf-155">В приведенном выше примере в словарь `ViewData` добавляется свойство `Title`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="d6baf-156">Свойство `Title` используется в файле _Pages/Shared/_Layout.cshtml\*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="d6baf-157">Ниже показаны первые несколько строк файла *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="d6baf-158">Строка `@*Markup removed for brevity.*@` представляет собой комментарий Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="d6baf-159">В отличие от комментариев HTML `<!-- -->` комментарии Razor не отправляются клиенту.</span><span class="sxs-lookup"><span data-stu-id="d6baf-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="d6baf-160">Дополнительные сведения см. в [веб-документации MDN. Начало работы с HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments).</span><span class="sxs-lookup"><span data-stu-id="d6baf-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="d6baf-161">Обновление макета</span><span class="sxs-lookup"><span data-stu-id="d6baf-161">Update the layout</span></span>

1. <span data-ttu-id="d6baf-162">Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="d6baf-163">Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="d6baf-164">Измените указанный выше элемент на следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="d6baf-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="d6baf-165">Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="d6baf-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="d6baf-166">В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6baf-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="d6baf-167">Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="d6baf-168">Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке.</span><span class="sxs-lookup"><span data-stu-id="d6baf-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="d6baf-169">Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="d6baf-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="d6baf-170">Сохраните изменения и протестируйте приложение, выбрав ссылку **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="d6baf-171">Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) в GitHub.</span><span class="sxs-lookup"><span data-stu-id="d6baf-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="d6baf-172">Проверьте ссылки **Home**, **RpMovie**, **Create**, **Edit** и **Delete**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="d6baf-173">Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.</span><span class="sxs-lookup"><span data-stu-id="d6baf-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="d6baf-174">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="d6baf-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d6baf-175">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации приложения.</span><span class="sxs-lookup"><span data-stu-id="d6baf-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="d6baf-176">Инструкции по добавлению десятичной запятой см. в [вопросе № 4076 на сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d6baf-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="d6baf-177">Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="d6baf-178">Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="d6baf-179">Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="d6baf-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="d6baf-180">Страничная модель Create</span><span class="sxs-lookup"><span data-stu-id="d6baf-180">The Create page model</span></span>

<span data-ttu-id="d6baf-181">Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="d6baf-182">Метод `OnGet` инициализирует все состояния, необходимые для страницы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="d6baf-183">Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="d6baf-184">Далее в этом руководстве показан пример инициализации состояния `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="d6baf-185">Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="d6baf-186">Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="d6baf-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d6baf-187">Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="d6baf-188">Метод `OnPostAsync` выполняется, когда страница публикует данные формы:</span><span class="sxs-lookup"><span data-stu-id="d6baf-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="d6baf-189">Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="d6baf-190">Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="d6baf-191">Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату.</span><span class="sxs-lookup"><span data-stu-id="d6baf-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="d6baf-192">Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="d6baf-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="d6baf-193">Если нет ошибок модели:</span><span class="sxs-lookup"><span data-stu-id="d6baf-193">If there are no model errors:</span></span>

* <span data-ttu-id="d6baf-194">Данные сохранены.</span><span class="sxs-lookup"><span data-stu-id="d6baf-194">The data is saved.</span></span>
* <span data-ttu-id="d6baf-195">Браузер перенаправляется на страницу Index (Индекс).</span><span class="sxs-lookup"><span data-stu-id="d6baf-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="d6baf-196">Страница Razor создания</span><span class="sxs-lookup"><span data-stu-id="d6baf-196">The Create Razor Page</span></span>

<span data-ttu-id="d6baf-197">Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="d6baf-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6baf-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d6baf-199">Visual Studio выделяет следующие теги полужирным шрифтом, который используется для вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="d6baf-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Представление страницы Create.cshtml в VS17](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d6baf-201">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d6baf-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d6baf-202">Следующие вспомогательные функции тегов показаны в предыдущей разметке.</span><span class="sxs-lookup"><span data-stu-id="d6baf-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="d6baf-203">Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6baf-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="d6baf-204">Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="d6baf-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="d6baf-205">Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида:</span><span class="sxs-lookup"><span data-stu-id="d6baf-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="d6baf-206">[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="d6baf-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="d6baf-207">Более подробно проверка рассматривается далее в этой серии статей.</span><span class="sxs-lookup"><span data-stu-id="d6baf-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="d6baf-208">[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `[for]` для свойства `Title`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="d6baf-209">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d6baf-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="d6baf-210">Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="d6baf-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6baf-211">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d6baf-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d6baf-212">[Предыдущая статья. Добавление модели](xref:tutorials/razor-pages/model)
> [Следующая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="d6baf-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="d6baf-213">Страницы Create, Delete, Details и Edit</span><span class="sxs-lookup"><span data-stu-id="d6baf-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="d6baf-214">Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="d6baf-215">Razor Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="d6baf-216">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="d6baf-217">Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="d6baf-218">Сформированные страницы следуют этому шаблону.</span><span class="sxs-lookup"><span data-stu-id="d6baf-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="d6baf-219">Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="d6baf-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="d6baf-220">Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов.</span><span class="sxs-lookup"><span data-stu-id="d6baf-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="d6baf-221">`OnGetAsync` или `OnGet` вызываются на странице Razor для инициализации состояния для страницы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="d6baf-222">В этом случае `OnGetAsync` возвращает список фильмов для отображения.</span><span class="sxs-lookup"><span data-stu-id="d6baf-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="d6baf-223">Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, тогда метод return не используется.</span><span class="sxs-lookup"><span data-stu-id="d6baf-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="d6baf-224">Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return.</span><span class="sxs-lookup"><span data-stu-id="d6baf-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="d6baf-225">Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="d6baf-226">Изучите страницу *Pages/Movies/Index.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="d6baf-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="d6baf-227">Razor может выполнять переход с HTML на C# или на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="d6baf-228">Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="d6baf-229">Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="d6baf-230">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="d6baf-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="d6baf-231">`@page` — это пример перехода на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="d6baf-232">Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).</span><span class="sxs-lookup"><span data-stu-id="d6baf-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="d6baf-233">директиву @model</span><span class="sxs-lookup"><span data-stu-id="d6baf-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="d6baf-234">Директива `@model` определяет тип модели, передаваемой на страницу Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="d6baf-235">В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="d6baf-236">Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="d6baf-237">Вспомогательные средства HTML</span><span class="sxs-lookup"><span data-stu-id="d6baf-237">HTML Helpers</span></span>

<span data-ttu-id="d6baf-238">Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:</span><span class="sxs-lookup"><span data-stu-id="d6baf-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="d6baf-239">Вспомогательный метод HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя.</span><span class="sxs-lookup"><span data-stu-id="d6baf-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="d6baf-240">Лямбда-выражение проверяется, а не вычисляется.</span><span class="sxs-lookup"><span data-stu-id="d6baf-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="d6baf-241">Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются.</span><span class="sxs-lookup"><span data-stu-id="d6baf-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="d6baf-242">При вычислении лямбда-выражения, например с помощью `@Html.DisplayFor(modelItem => item.Title)`, вычисляются значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="d6baf-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="d6baf-243">Страница макета</span><span class="sxs-lookup"><span data-stu-id="d6baf-243">The layout page</span></span>

<span data-ttu-id="d6baf-244">Выберите ссылки в меню **RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="d6baf-245">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="d6baf-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="d6baf-246">Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d6baf-247">Откройте и проверьте файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d6baf-248">С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="d6baf-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="d6baf-249">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d6baf-250">`RenderBody` — это заполнитель, в котором отображаются все создаваемые представления для определенных страниц, *упакованные* в страницу макета.</span><span class="sxs-lookup"><span data-stu-id="d6baf-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d6baf-251">Например, если щелкнуть ссылку **Конфиденциальность**, представление **Pages/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="d6baf-252">ViewData и макет</span><span class="sxs-lookup"><span data-stu-id="d6baf-252">ViewData and layout</span></span>

<span data-ttu-id="d6baf-253">Рассмотрим следующий код из файла *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="d6baf-254">Выделенный выше код представляет собой пример перехода Razor на C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="d6baf-255">Символы `{` и `}` ограничивают блок кода C#.</span><span class="sxs-lookup"><span data-stu-id="d6baf-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="d6baf-256">Базовый класс `PageModel` содержит свойство словаря `ViewData`, позволяющее передать данные в представление.</span><span class="sxs-lookup"><span data-stu-id="d6baf-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="d6baf-257">Объекты можно добавить в словарь `ViewData` с помощью шаблона "ключ/значение".</span><span class="sxs-lookup"><span data-stu-id="d6baf-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="d6baf-258">В приведенном выше примере в словарь `ViewData` добавляется свойство `Title`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="d6baf-259">Свойство `Title` используется в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d6baf-260">Ниже показаны первые несколько строк файла *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="d6baf-261">Строка `@*Markup removed for brevity.*@` представляет собой комментарий Razor.</span><span class="sxs-lookup"><span data-stu-id="d6baf-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="d6baf-262">В отличие от комментариев HTML `<!-- -->` комментарии Razor не отправляются клиенту.</span><span class="sxs-lookup"><span data-stu-id="d6baf-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="d6baf-263">Дополнительные сведения см. в [веб-документации MDN. Начало работы с HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments).</span><span class="sxs-lookup"><span data-stu-id="d6baf-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="d6baf-264">Обновление макета</span><span class="sxs-lookup"><span data-stu-id="d6baf-264">Update the layout</span></span>

<span data-ttu-id="d6baf-265">Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="d6baf-266">Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="d6baf-267">Замените указанный выше элемент на следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="d6baf-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="d6baf-268">Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="d6baf-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="d6baf-269">В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6baf-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="d6baf-270">Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="d6baf-271">Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке.</span><span class="sxs-lookup"><span data-stu-id="d6baf-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="d6baf-272">Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="d6baf-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="d6baf-273">Сохраните изменения и протестируйте приложение, нажав на ссылку **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="d6baf-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="d6baf-274">Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) в GitHub.</span><span class="sxs-lookup"><span data-stu-id="d6baf-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="d6baf-275">Проверьте другие ссылки (**Home**, **RpMovie**, **Create**, **Edit** и **Delete**).</span><span class="sxs-lookup"><span data-stu-id="d6baf-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="d6baf-276">Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.</span><span class="sxs-lookup"><span data-stu-id="d6baf-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="d6baf-277">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="d6baf-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d6baf-278">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации приложения.</span><span class="sxs-lookup"><span data-stu-id="d6baf-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="d6baf-279">Инструкции по добавлению десятичной запятой представлены в этом [вопросе 4076 GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d6baf-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="d6baf-280">Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="d6baf-281">Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="d6baf-282">Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="d6baf-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="d6baf-283">Страничная модель Create</span><span class="sxs-lookup"><span data-stu-id="d6baf-283">The Create page model</span></span>

<span data-ttu-id="d6baf-284">Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="d6baf-285">Метод `OnGet` инициализирует все состояния, необходимые для страницы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="d6baf-286">Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="d6baf-287">Далее в этом руководстве вы увидите, как метод `OnGet` инициализирует состояние.</span><span class="sxs-lookup"><span data-stu-id="d6baf-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="d6baf-288">Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d6baf-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="d6baf-289">Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут [BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute>.</span><span class="sxs-lookup"><span data-stu-id="d6baf-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d6baf-290">Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="d6baf-291">Метод `OnPostAsync` выполняется, когда страница публикует данные формы:</span><span class="sxs-lookup"><span data-stu-id="d6baf-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="d6baf-292">Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="d6baf-293">Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы.</span><span class="sxs-lookup"><span data-stu-id="d6baf-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="d6baf-294">Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату.</span><span class="sxs-lookup"><span data-stu-id="d6baf-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="d6baf-295">Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="d6baf-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="d6baf-296">Если ошибок в модели нет, данные сохраняются, а браузер переадресовывается на страницу Index (Индексы).</span><span class="sxs-lookup"><span data-stu-id="d6baf-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="d6baf-297">Страница Razor создания</span><span class="sxs-lookup"><span data-stu-id="d6baf-297">The Create Razor Page</span></span>

<span data-ttu-id="d6baf-298">Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d6baf-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="d6baf-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6baf-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d6baf-300">Visual Studio выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов:</span><span class="sxs-lookup"><span data-stu-id="d6baf-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Представление страницы Create.cshtml в VS17](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d6baf-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6baf-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d6baf-303">Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="d6baf-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d6baf-304">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d6baf-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d6baf-305">Visual Studio для Mac выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="d6baf-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="d6baf-306">Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6baf-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="d6baf-307">Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="d6baf-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="d6baf-308">Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида:</span><span class="sxs-lookup"><span data-stu-id="d6baf-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="d6baf-309">[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="d6baf-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="d6baf-310">Более подробно проверка рассматривается далее в этой серии статей.</span><span class="sxs-lookup"><span data-stu-id="d6baf-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="d6baf-311">[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `[for]` для свойства `Title`.</span><span class="sxs-lookup"><span data-stu-id="d6baf-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="d6baf-312">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d6baf-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6baf-313">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d6baf-313">Additional resources</span></span>

* [<span data-ttu-id="d6baf-314">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="d6baf-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="d6baf-315">[Предыдущая статья. Добавление модели](xref:tutorials/razor-pages/model)
> [Следующая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="d6baf-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
