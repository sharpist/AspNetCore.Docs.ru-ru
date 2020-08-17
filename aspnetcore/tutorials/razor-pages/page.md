---
title: Часть 3. Razor Pages, созданные путем формирования шаблонов, в ASP.NET Core
author: rick-anderson
description: Часть 3 серии руководств по Razor Pages.
ms.author: riande
ms.date: 08/17/2019
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: f8942e52b3b438817e3d1041a2c6b568eb662469
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020396"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Часть 3. Razor Pages, созданные путем формирования шаблонов, в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Этот учебник описывает Razor Pages, созданные путем формирования шаблонов в [предыдущем учебнике](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Страницы Create, Delete, Details и Edit

Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Pages являются производными от класса `PageModel`. Как правило, класс, производный от `PageModel`, называется `<PageName>Model`. Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`. Этому шаблону соответствуют все сформированные страницы. Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).

Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов. Для инициализации состояния страницы вызывается `OnGetAsync` или `OnGet`. В этом случае `OnGetAsync` возвращает список фильмов для отображения.

Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, оператор return не используется. Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return. Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Изучите страницу Razor *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor может выполнять переход с HTML на C# или на разметку Razor. Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.

### <a name="the-page-directive"></a>директиву @page

Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы. `@page` должна быть первой директивой Razor на странице. `@page` — это пример перехода на разметку Razor. Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).

Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя. Лямбда-выражение проверяется, а не вычисляется. Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются. При вычислении лямбда-выражения (например, с помощью `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.

<a name="md"></a>

### <a name="the-model-directive"></a>директиву @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Директива `@model` определяет тип модели, передаваемой на страницу Razor. В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor. Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.

### <a name="the-layout-page"></a>Страница макета

Выберите ссылки в меню ( **RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**). Меню на каждой странице имеют одинаковый макет. Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*. Откройте файл *Pages/Shared/_Layout.cshtml*.

Шаблоны [макета](xref:mvc/views/layout) позволяют сделать следующее для макета контейнера HTML:

* указать его в одном расположении;
* применить его на нескольких страницах сайта.

Найдите строку `@RenderBody()`. `RenderBody` — это заполнитель для отображения всех представлений определенных страниц, *упакованных* в страницу макета. Например, щелкните ссылку **Конфиденциальность**, и представление *Pages/Privacy.cshtml* отобразится в методе `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData и макет

Рассмотрим следующую разметку из файла *Pages/Movies/Index.cshtml*.

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Выделенная выше разметка представляет собой пример перехода Razor на C#. Символы `{` и `}` ограничивают блок кода C#.

Базовый класс `PageModel` содержит свойство словаря `ViewData`. Оно позволяет передать данные в представление. Объекты можно добавить в словарь `ViewData` с помощью шаблона "ключ/значение". В приведенном выше примере в словарь `ViewData` добавляется свойство `"Title"`.

Свойство `"Title"` используется в файле *Pages/Shared/_Layout.cshtml*. Ниже показаны первые несколько строк файла *_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Строка `@*Markup removed for brevity.*@` представляет собой комментарий Razor. В отличие от комментариев HTML (`<!-- -->`) комментарии Razor не отправляются клиенту.

### <a name="update-the-layout"></a>Обновление макета

Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Измените указанный выше элемент на следующую разметку.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro). В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`. Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке. Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).

Сохраните изменения и протестируйте приложение, нажав на ссылку **RpMovie**. Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) в GitHub.

Проверьте другие ссылки (**Home**, **RpMovie**, **Create**, **Edit** и **Delete**). Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.

> [!NOTE]
> В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации вашего приложения. Инструкции по добавлению десятичной запятой см. в [вопросе № 4076 на сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*. Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Страничная модель Create

Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Метод `OnGet` инициализирует все состояния, необходимые для страницы. Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`. Далее в этом руководстве показан пример инициализации состояния `OnGet`. Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.

Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут `[BindProperty]`. Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.

Метод `OnPostAsync` выполняется, когда страница публикует данные формы:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы. Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы. Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату. Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.

Если ошибок в модели нет, данные сохраняются, а браузер переадресуется на страницу индексов.

### <a name="the-create-no-locrazor-page"></a>Страница Razor создания

Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio выделяет следующие теги полужирным шрифтом, который используется для вспомогательных функций тегов.

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Представление страницы Create.cshtml в VS17](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Следующие вспомогательные функции тегов показаны в предыдущей разметке.

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Visual Studio выделяет следующие теги полужирным шрифтом, который используется для вспомогательных функций тегов.

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper). Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).

Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки. Более подробно проверка рассматривается далее в этой серии статей.

[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `for` для свойства `Title`.

[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.

Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Добавление метода](xref:tutorials/razor-pages/model)
> [Далее: база данных](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Этот учебник описывает Razor Pages, созданные путем формирования шаблонов в [предыдущем учебнике](xref:tutorials/razor-pages/model).

[Просмотрите или скачайте](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) пример.

## <a name="the-create-delete-details-and-edit-pages"></a>Страницы Create, Delete, Details и Edit

Изучите модель страницы *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Pages являются производными от класса `PageModel`. Как правило, класс, производный от `PageModel`, называется `<PageName>Model`. Используя [внедрение зависимостей](xref:fundamentals/dependency-injection), конструктор добавляет на страницу `RazorPagesMovieContext`. Этому шаблону соответствуют все сформированные страницы. Дополнительные сведения об асинхронном программировании с использованием Entity Framework см. в разделе [Асинхронный код](xref:data/ef-rp/intro#asynchronous-code).

Когда к странице направляется запрос, метод `OnGetAsync` возвращает на страницу Razor список фильмов. `OnGetAsync` или `OnGet` вызываются на странице Razor для инициализации состояния для страницы. В этом случае `OnGetAsync` возвращает список фильмов для отображения.

Когда `OnGet` возвращает `void` или `OnGetAsync` возвращает `Task`, возвращаемый метод не используется. Если возвращаемый тип — `IActionResult` или `Task<IActionResult>`, необходимо предоставить оператор return. Например, метод `OnPostAsync` в *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Изучите страницу Razor *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor может выполнять переход с HTML на C# или на разметку Razor. Если за символом `@` следует [зарезервированное ключевое слово Razor](xref:mvc/views/razor#razor-reserved-keywords), он переходит на разметку Razor, а если нет, то на C#.

Директива Razor `@page` преобразует файл в действие MVC, а значит, он может обрабатывать запросы. `@page` должна быть первой директивой Razor на странице. `@page` — это пример перехода на разметку Razor. Дополнительные сведения см. в статье [Синтаксис Razor](xref:mvc/views/razor#razor-syntax).

Проверьте лямбда-выражение, которое используется в следующем вспомогательном методе HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Вспомогательный метод HTML `DisplayNameFor` проверяет свойство `Title`, указанное в лямбда-выражении, и определяет отображаемое имя. Лямбда-выражение проверяется, а не вычисляется. Это означает, что в случае, если `model`, `model.Movie` или `model.Movie[0]` имеют значение `null` или пусты, права доступа не нарушаются. При вычислении лямбда-выражения (например, с помощью `@Html.DisplayFor(modelItem => item.Title)`) вычисляются значения для свойств модели.

<a name="md"></a>

### <a name="the-model-directive"></a>директиву @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Директива `@model` определяет тип модели, передаваемой на страницу Razor. В приведенном выше примере строка `@model` делает класс, производный от `PageModel`, доступным для страницы Razor. Модель используется на странице во [вспомогательных методах HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` и `@Html.DisplayFor`.

### <a name="the-layout-page"></a>Страница макета

Выберите ссылки в меню ( **RazorPagesMovie**, **Домашняя страница** и **Конфиденциальность**). Меню на каждой странице имеют одинаковый макет. Макет меню реализован в файле *Pages/Shared/_Layout.cshtml*. Откройте файл *Pages/Shared/_Layout.cshtml*.

С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта. Найдите строку `@RenderBody()`. `RenderBody` — это заполнитель, в котором отображаются все создаваемые представления для определенных страниц, *упакованные* в страницу макета. Например, если щелкнуть ссылку **Конфиденциальность**, представление **Pages/Privacy.cshtml** отображается в методе `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData и макет

Рассмотрим следующий код из файла *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Выделенный выше код представляет собой пример перехода Razor на C#. Символы `{` и `}` ограничивают блок кода C#.

Базовый класс `PageModel` содержит свойство словаря `ViewData`, позволяющее передать данные в представление. Объекты можно добавить в словарь `ViewData` с помощью шаблона "ключ/значение". В приведенном выше примере в словарь `ViewData` добавляется свойство "Title".

Свойство "Title" используется в файле *Pages/Shared/_Layout.cshtml*. Ниже показаны первые несколько строк файла *_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Строка `@*Markup removed for brevity.*@` содержит комментарий Razor, который не отображается в файле макета. В отличие от комментариев HTML (`<!-- -->`) комментарии Razor не отправляются клиенту.

### <a name="update-the-layout"></a>Обновление макета

Измените элемент `<title>` в файле *Pages/Shared/_Layout.cshtml* так, чтобы вместо **Movie** отображалось **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Найдите следующий элемент привязки в файле *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Замените указанный выше элемент на следующую разметку.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Указанный выше элемент привязки является [вспомогательной функцией тега](xref:mvc/views/tag-helpers/intro). В данном случае он является [вспомогательной функцией тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Атрибут вспомогательной функции тега `asp-page="/Movies/Index"` и его значение создают ссылку на страницу Razor `/Movies/Index`. Атрибут `asp-area` имеет пустое значение, поэтому эта область не используется в ссылке. Дополнительные сведения см. в статье [Области](xref:mvc/controllers/areas).

Сохраните изменения и протестируйте приложение, нажав на ссылку **RpMovie**. Если у вас возникли проблемы, ознакомьтесь с файлом [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) в GitHub.

Проверьте другие ссылки (**Home**, **RpMovie**, **Create**, **Edit** и **Delete**). Каждая страница задает заголовок, который отображается на вкладке браузера. При добавлении страницы в избранное заголовок используется в закладках.

> [!NOTE]
> В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (","), а для отображения данных в форматах для других языков, кроме английского, выполните действия, необходимые для глобализации вашего приложения. Инструкции по добавлению десятичной запятой представлены в этом [вопросе 4076 GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

Свойство `Layout` определяется в файле *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Представленный выше код задает файл разметки *Pages/Shared/_Layout.cshtml* для всех файлов Razor в папке *Pages*. Дополнительные сведения см. в статье о [макете](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Страничная модель Create

Изучите страничную модель *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Метод `OnGet` инициализирует все состояния, необходимые для страницы. Страница Create не содержит никаких состояний для инициализации, поэтому возвращается `Page`. Далее в этом руководстве вы увидите, как метод `OnGet` инициализирует состояние. Метод `Page` создает объект `PageResult`, который формирует страницу *Create.cshtml*.

Для указания согласия на [привязку модели](xref:mvc/models/model-binding) в свойстве `Movie` используется атрибут `[BindProperty]`. Когда форма Create публикует свои значения, среда выполнения ASP.NET Core связывает переданные значения с моделью `Movie`.

Метод `OnPostAsync` выполняется, когда страница публикует данные формы:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Если в модели есть ошибки, форма отображается снова вместе со всеми опубликованными данными этой формы. Большинство ошибок в модели может быть перехвачено на стороне клиента до публикации формы. Пример ошибки в модели — это публикация значения для поля даты, которое нельзя конвертировать в дату. Проверка на стороне клиента и проверка модели обсуждаются подробнее далее в этом учебнике.

Если ошибок в модели нет, данные сохраняются, а браузер переадресуется на страницу индексов.

### <a name="the-create-no-locrazor-page"></a>Страница Razor создания

Изучите файл страницы Razor *Pages/Movies/Create.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов:

![Представление страницы Create.cshtml в VS17](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Дополнительные сведения о вспомогательных функциях тегов, таких как `<form method="post">`, см. в статье [Вспомогательные функции тегов в ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Visual Studio для Mac выделяет тег `<form method="post">` полужирным шрифтом, который используется для вспомогательных функций тегов.

---

Элемент `<form method="post">` представляет собой [вспомогательную функцию тега Form](xref:mvc/views/working-with-forms#the-form-tag-helper). Вспомогательная функция тега Form автоматически включает [маркер защиты от подделки](xref:security/anti-request-forgery).

Ядро формирования шаблонов создает разметку Razor для каждого поля в модели (кроме ID) следующего вида.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Вспомогательные функции тегов Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` и `<span asp-validation-for`) отображают ошибки проверки. Более подробно проверка рассматривается далее в этой серии статей.

[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) создает подпись к метке и атрибут `for` для свойства `Title`.

[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) использует атрибуты [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) и создает HTML-атрибуты, необходимые для проверки jQuery на стороне клиента.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Предыдущая статья. Добавление метода](xref:tutorials/razor-pages/model)
> [Далее: база данных](xref:tutorials/razor-pages/sql)

::: moniker-end
