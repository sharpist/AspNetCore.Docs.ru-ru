---
title: Часть 3. Razor Pages, созданные путем формирования шаблонов, в ASP.NET Core
author: rick-anderson
description: Часть 3 серии руководств по Razor Pages.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 898f3be045755a0ee14c738b6ffc104408ded7b6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407776"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="fb21c-103">Часть 3. Razor Pages, созданные путем формирования шаблонов, в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb21c-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb21c-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="fb21c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb21c-105">Этот учебник описывает Razor Pages, созданные путем формирования шаблонов в [предыдущем учебнике](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="fb21c-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="fb21c-106">Страницы Create, Delete, Details и Edit</span><span class="sxs-lookup"><span data-stu-id="fb21c-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="fb21c-107">Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="fb21c-108"> Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-108"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="fb21c-109">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="fb21c-110">Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="fb21c-111">Этому шаблону соответствуют все сформированные страницы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="fb21c-112">Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="fb21c-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="fb21c-113">Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="fb21c-114">Для инициализации состояния страницы вызывается `OnGetAsync` или `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="fb21c-115">В этом случае `OnGetAsync` возвращает список фильмов для отображения.</span><span class="sxs-lookup"><span data-stu-id="fb21c-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="fb21c-116">Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, оператор return не используется.</span><span class="sxs-lookup"><span data-stu-id="fb21c-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="fb21c-117">Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return.</span><span class="sxs-lookup"><span data-stu-id="fb21c-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="fb21c-118">Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="fb21c-119">Изучите страницу Razor *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="fb21c-120"> может выполнять переход с HTML на C# или на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-120"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="fb21c-121">Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="fb21c-122">директиву @page</span><span class="sxs-lookup"><span data-stu-id="fb21c-122">The @page directive</span></span>

<span data-ttu-id="fb21c-123">Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="fb21c-124">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="fb21c-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="fb21c-125">`@page` — это пример перехода на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="fb21c-126">Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).</span><span class="sxs-lookup"><span data-stu-id="fb21c-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="fb21c-127">Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:</span><span class="sxs-lookup"><span data-stu-id="fb21c-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="fb21c-128">Вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя.</span><span class="sxs-lookup"><span data-stu-id="fb21c-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="fb21c-129">Лямбда-выражение проверяется, а не вычисляется.</span><span class="sxs-lookup"><span data-stu-id="fb21c-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="fb21c-130">Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются.</span><span class="sxs-lookup"><span data-stu-id="fb21c-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="fb21c-131">При вычислении лямбда-выражения (например, с помощью `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="fb21c-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="fb21c-132">директиву @model</span><span class="sxs-lookup"><span data-stu-id="fb21c-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="fb21c-133">Директива `@model` определяет тип модели, передаваемой на страницу Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="fb21c-134">В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="fb21c-135">Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="fb21c-136">Страница макета</span><span class="sxs-lookup"><span data-stu-id="fb21c-136">The layout page</span></span>

<span data-ttu-id="fb21c-137">Выберите ссылки в меню (**RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**).</span><span class="sxs-lookup"><span data-stu-id="fb21c-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="fb21c-138">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="fb21c-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="fb21c-139">Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="fb21c-140">Откройте файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="fb21c-141">Шаблоны [макета](xref:mvc/views/layout) позволяют сделать следующее для макета контейнера HTML:</span><span class="sxs-lookup"><span data-stu-id="fb21c-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="fb21c-142">указать его в одном расположении;</span><span class="sxs-lookup"><span data-stu-id="fb21c-142">Specified in one place.</span></span>
* <span data-ttu-id="fb21c-143">применить его на нескольких страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="fb21c-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="fb21c-144">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="fb21c-145">`RenderBody` — это заполнитель для отображения всех представлений определенных страниц, *упакованных* в страницу макета.</span><span class="sxs-lookup"><span data-stu-id="fb21c-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="fb21c-146">Например, щелкните ссылку **Конфиденциальность**, и представление *Pages/Privacy.cshtml* отобразится в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="fb21c-147">ViewData и макет</span><span class="sxs-lookup"><span data-stu-id="fb21c-147">ViewData and layout</span></span>

<span data-ttu-id="fb21c-148">Рассмотрим следующую разметку из файла *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="fb21c-149">Выделенная выше разметка представляет собой пример перехода Razor на C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="fb21c-150">Символы `{` и `}` ограничивают блок кода C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="fb21c-151">Базовый класс `PageModel` содержит свойство словаря `ViewData`. Оно позволяет передать данные в представление.</span><span class="sxs-lookup"><span data-stu-id="fb21c-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="fb21c-152">Объекты можно добавить в словарь `ViewData` с помощью шаблона "ключ/значение".</span><span class="sxs-lookup"><span data-stu-id="fb21c-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="fb21c-153">В приведенном выше примере в словарь `ViewData` добавляется свойство `"Title"`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="fb21c-154">Свойство `"Title"` используется в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="fb21c-155">Ниже показаны первые несколько строк файла *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="fb21c-156">Строка `@*Markup removed for brevity.*@` представляет собой комментарий Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="fb21c-157">В отличие от комментариев HTML (`<!-- -->`) комментарии Razor не отправляются клиенту.</span><span class="sxs-lookup"><span data-stu-id="fb21c-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="fb21c-158">Обновление макета</span><span class="sxs-lookup"><span data-stu-id="fb21c-158">Update the layout</span></span>

<span data-ttu-id="fb21c-159">Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fb21c-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="fb21c-160">Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="fb21c-161">Измените указанный выше элемент на следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="fb21c-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="fb21c-162">Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="fb21c-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="fb21c-163">В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fb21c-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="fb21c-164">Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="fb21c-165">Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке.</span><span class="sxs-lookup"><span data-stu-id="fb21c-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="fb21c-166">Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="fb21c-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="fb21c-167">Сохраните изменения и протестируйте приложение, нажав на ссылку **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="fb21c-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="fb21c-168">Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) в GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb21c-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="fb21c-169">Проверьте другие ссылки (**Home**, **RpMovie**, **Create**, **Edit** и **Delete**).</span><span class="sxs-lookup"><span data-stu-id="fb21c-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="fb21c-170">Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.</span><span class="sxs-lookup"><span data-stu-id="fb21c-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="fb21c-171">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="fb21c-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fb21c-172">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="fb21c-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="fb21c-173">Инструкции по добавлению десятичной запятой см. в [вопросе № 4076 на сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="fb21c-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="fb21c-174">Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="fb21c-175">Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="fb21c-176">Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="fb21c-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="fb21c-177">Страничная модель Create</span><span class="sxs-lookup"><span data-stu-id="fb21c-177">The Create page model</span></span>

<span data-ttu-id="fb21c-178">Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="fb21c-179">Метод `OnGet` инициализирует все состояния, необходимые для страницы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="fb21c-180">Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="fb21c-181">Далее в этом руководстве показан пример инициализации состояния `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="fb21c-182">Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="fb21c-183">Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="fb21c-184">Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="fb21c-185">Метод `OnPostAsync` выполняется, когда страница публикует данные формы:</span><span class="sxs-lookup"><span data-stu-id="fb21c-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="fb21c-186">Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="fb21c-187">Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="fb21c-188">Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату.</span><span class="sxs-lookup"><span data-stu-id="fb21c-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="fb21c-189">Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="fb21c-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="fb21c-190">Если ошибок в модели нет, данные сохраняются, а браузер переадресуется на страницу индексов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="fb21c-191">Страница Razor создания</span><span class="sxs-lookup"><span data-stu-id="fb21c-191">The Create Razor Page</span></span>

<span data-ttu-id="fb21c-192">Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="fb21c-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb21c-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fb21c-194">Visual Studio выделяет следующие теги полужирным шрифтом, который используется для вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Представление страницы Create.cshtml в VS17](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb21c-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb21c-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fb21c-197">Следующие вспомогательные функции тегов показаны в предыдущей разметке.</span><span class="sxs-lookup"><span data-stu-id="fb21c-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb21c-198">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fb21c-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fb21c-199">Visual Studio выделяет следующие теги полужирным шрифтом, который используется для вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="fb21c-200">Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fb21c-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="fb21c-201">Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="fb21c-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="fb21c-202">Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида.</span><span class="sxs-lookup"><span data-stu-id="fb21c-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="fb21c-203">[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="fb21c-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="fb21c-204">Более подробно проверка рассматривается далее в этой серии статей.</span><span class="sxs-lookup"><span data-stu-id="fb21c-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="fb21c-205">[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `for` для свойства `Title`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="fb21c-206">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="fb21c-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="fb21c-207">Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="fb21c-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb21c-208">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fb21c-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fb21c-209">[Предыдущая статья. Добавление метода](xref:tutorials/razor-pages/model)
> [Далее: база данных](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="fb21c-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb21c-210">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="fb21c-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb21c-211">Этот учебник описывает Razor Pages, созданные путем формирования шаблонов в [предыдущем учебнике](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="fb21c-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="fb21c-212">[Просмотрите или скачайте](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) пример.</span><span class="sxs-lookup"><span data-stu-id="fb21c-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="fb21c-213">Страницы Create, Delete, Details и Edit</span><span class="sxs-lookup"><span data-stu-id="fb21c-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="fb21c-214">Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="fb21c-215"> Pages являются производными от класса `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-215"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="fb21c-216">Как правило, класс, производный от `PageModel`, называется `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="fb21c-217">Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="fb21c-218">Этому шаблону соответствуют все сформированные страницы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="fb21c-219">Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="fb21c-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="fb21c-220">Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="fb21c-221">`OnGetAsync` или `OnGet` вызываются на странице Razor для инициализации состояния для страницы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="fb21c-222">В этом случае `OnGetAsync` возвращает список фильмов для отображения.</span><span class="sxs-lookup"><span data-stu-id="fb21c-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="fb21c-223">Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, возвращаемый метод не используется.</span><span class="sxs-lookup"><span data-stu-id="fb21c-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="fb21c-224">Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return.</span><span class="sxs-lookup"><span data-stu-id="fb21c-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="fb21c-225">Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="fb21c-226">Изучите страницу Razor *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="fb21c-227"> может выполнять переход с HTML на C# или на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-227"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="fb21c-228">Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="fb21c-229">Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="fb21c-230">`@page` должна быть первой директивой Razor на странице.</span><span class="sxs-lookup"><span data-stu-id="fb21c-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="fb21c-231">`@page` — это пример перехода на разметку Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="fb21c-232">Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).</span><span class="sxs-lookup"><span data-stu-id="fb21c-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="fb21c-233">Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:</span><span class="sxs-lookup"><span data-stu-id="fb21c-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="fb21c-234">Вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя.</span><span class="sxs-lookup"><span data-stu-id="fb21c-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="fb21c-235">Лямбда-выражение проверяется, а не вычисляется.</span><span class="sxs-lookup"><span data-stu-id="fb21c-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="fb21c-236">Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются.</span><span class="sxs-lookup"><span data-stu-id="fb21c-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="fb21c-237">При вычислении лямбда-выражения (например, с помощью `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="fb21c-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="fb21c-238">директиву @model</span><span class="sxs-lookup"><span data-stu-id="fb21c-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="fb21c-239">Директива `@model` определяет тип модели, передаваемой на страницу Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="fb21c-240">В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="fb21c-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="fb21c-241">Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="fb21c-242">Страница макета</span><span class="sxs-lookup"><span data-stu-id="fb21c-242">The layout page</span></span>

<span data-ttu-id="fb21c-243">Выберите ссылки в меню (**RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**).</span><span class="sxs-lookup"><span data-stu-id="fb21c-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="fb21c-244">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="fb21c-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="fb21c-245">Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="fb21c-246">Откройте файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="fb21c-247">С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="fb21c-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="fb21c-248">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="fb21c-249">`RenderBody` — это заполнитель, в котором отображаются все создаваемые представления для определенных страниц, *упакованные* в страницу макета.</span><span class="sxs-lookup"><span data-stu-id="fb21c-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="fb21c-250">Например, если щелкнуть ссылку **Конфиденциальность**, представление **Pages/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="fb21c-251">ViewData и макет</span><span class="sxs-lookup"><span data-stu-id="fb21c-251">ViewData and layout</span></span>

<span data-ttu-id="fb21c-252">Рассмотрим следующий код из файла *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="fb21c-253">Выделенный выше код представляет собой пример перехода Razor на C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="fb21c-254">Символы `{` и `}` ограничивают блок кода C#.</span><span class="sxs-lookup"><span data-stu-id="fb21c-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="fb21c-255">Базовый класс `PageModel` содержит свойство словаря `ViewData`, позволяющее передать данные в представление.</span><span class="sxs-lookup"><span data-stu-id="fb21c-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="fb21c-256">Объекты можно добавить в словарь `ViewData` с помощью шаблона "ключ/значение".</span><span class="sxs-lookup"><span data-stu-id="fb21c-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="fb21c-257">В приведенном выше примере в словарь `ViewData` добавляется свойство "Title".</span><span class="sxs-lookup"><span data-stu-id="fb21c-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="fb21c-258">Свойство "Title" используется в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="fb21c-259">Ниже показаны первые несколько строк файла *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="fb21c-260">Строка `@*Markup removed for brevity.*@` содержит комментарий Razor, который не отображается в файле макета.</span><span class="sxs-lookup"><span data-stu-id="fb21c-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="fb21c-261">В отличие от комментариев HTML (`<!-- -->`) комментарии Razor не отправляются клиенту.</span><span class="sxs-lookup"><span data-stu-id="fb21c-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="fb21c-262">Обновление макета</span><span class="sxs-lookup"><span data-stu-id="fb21c-262">Update the layout</span></span>

<span data-ttu-id="fb21c-263">Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fb21c-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="fb21c-264">Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="fb21c-265">Замените указанный выше элемент на следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="fb21c-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="fb21c-266">Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="fb21c-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="fb21c-267">В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fb21c-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="fb21c-268">Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="fb21c-269">Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке.</span><span class="sxs-lookup"><span data-stu-id="fb21c-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="fb21c-270">Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="fb21c-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="fb21c-271">Сохраните изменения и протестируйте приложение, нажав на ссылку **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="fb21c-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="fb21c-272">Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) в GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb21c-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="fb21c-273">Проверьте другие ссылки (**Home**, **RpMovie**, **Create**, **Edit** и **Delete**).</span><span class="sxs-lookup"><span data-stu-id="fb21c-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="fb21c-274">Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.</span><span class="sxs-lookup"><span data-stu-id="fb21c-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="fb21c-275">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="fb21c-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fb21c-276">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="fb21c-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="fb21c-277">Инструкции по добавлению десятичной запятой представлены в этом [вопросе 4076 GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="fb21c-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="fb21c-278">Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="fb21c-279">Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="fb21c-280">Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="fb21c-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="fb21c-281">Страничная модель Create</span><span class="sxs-lookup"><span data-stu-id="fb21c-281">The Create page model</span></span>

<span data-ttu-id="fb21c-282">Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="fb21c-283">Метод `OnGet` инициализирует все состояния, необходимые для страницы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="fb21c-284">Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="fb21c-285">Далее в этом руководстве вы увидите, как метод `OnGet` инициализирует состояние.</span><span class="sxs-lookup"><span data-stu-id="fb21c-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="fb21c-286">Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb21c-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="fb21c-287">Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут `[BindProperty]`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="fb21c-288">Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="fb21c-289">Метод `OnPostAsync` выполняется, когда страница публикует данные формы:</span><span class="sxs-lookup"><span data-stu-id="fb21c-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="fb21c-290">Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="fb21c-291">Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы.</span><span class="sxs-lookup"><span data-stu-id="fb21c-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="fb21c-292">Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату.</span><span class="sxs-lookup"><span data-stu-id="fb21c-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="fb21c-293">Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="fb21c-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="fb21c-294">Если ошибок в модели нет, данные сохраняются, а браузер переадресуется на страницу индексов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="fb21c-295">Страница Razor создания</span><span class="sxs-lookup"><span data-stu-id="fb21c-295">The Create Razor Page</span></span>

<span data-ttu-id="fb21c-296">Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb21c-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="fb21c-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb21c-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fb21c-298">Visual Studio выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов:</span><span class="sxs-lookup"><span data-stu-id="fb21c-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Представление страницы Create.cshtml в VS17](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb21c-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb21c-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fb21c-301">Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="fb21c-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb21c-302">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fb21c-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fb21c-303">Visual Studio для Mac выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="fb21c-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="fb21c-304">Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fb21c-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="fb21c-305">Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="fb21c-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="fb21c-306">Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида.</span><span class="sxs-lookup"><span data-stu-id="fb21c-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="fb21c-307">[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="fb21c-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="fb21c-308">Более подробно проверка рассматривается далее в этой серии статей.</span><span class="sxs-lookup"><span data-stu-id="fb21c-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="fb21c-309">[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `for` для свойства `Title`.</span><span class="sxs-lookup"><span data-stu-id="fb21c-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="fb21c-310">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="fb21c-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb21c-311">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fb21c-311">Additional resources</span></span>

* [<span data-ttu-id="fb21c-312">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="fb21c-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="fb21c-313">[Предыдущая статья. Добавление метода](xref:tutorials/razor-pages/model)
> [Далее: база данных](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="fb21c-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
