---
title: Часть 5. Изменение созданных страниц в приложении ASP.NET Core
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages.
ms.author: riande
ms.date: 12/20/2018
no-loc:
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
ms.openlocfilehash: 7d25dae67c928fa659654ce4ab34cfdad08b5300
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060070"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Часть 5. Изменение созданных страниц в приложении ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала. Вместо **ReleaseDate** должно стоять **Дата выпуска** (два слова).

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Обновление созданного кода

Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных. Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).

Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве. Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate"). Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.

Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit** , чтобы увидеть конечный URL-адрес.

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Ссылки **Edit** , **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor. В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе страницы Razor (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`). Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).

Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере. Ниже показана часть созданного кода HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса (например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`).

### <a name="add-route-template"></a>Добавление шаблона маршрута

Обновите страницы Razor Edit, Details и Delete так, чтобы использовался шаблон маршрута "{id:int}". Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`. Запустите приложение и просмотрите исходный код. Созданный код HTML добавляет идентификатор в путь URL-адреса:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено). Например, `http://localhost:5000/Movies/Details` приведет к ошибке 404. Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:

 ```cshtml
@page "{id:int?}"
```

Чтобы проверить поведение `@page "{id:int?}"`:

* Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.
* Установите точку останова `public async Task<IActionResult> OnGetAsync(int? id)` (в *Pages/Movies/Details.cshtml.cs* ).
* Перейдите к `https://localhost:5001/Movies/Details/`.

Из-за директивы `@page "{id:int}"` точка останова не достигается. Механизм маршрутизации возвращает ошибку HTTP 404. При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Проверка обработки исключений нежесткой блокировки

Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.

Чтобы протестировать блок `catch`, выполните указанные ниже действия.

* Задайте точку останова в `catch (DbUpdateConcurrencyException)`
* Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.
* В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.
* В первом окне браузера опубликуйте изменения для фильма.

Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма. Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Проверка публикации и привязки

Изучите файл *Pages/Movies/Edit.cshtml.cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

При выполнении HTTP-запроса GET к странице Movies/Edit (например, `http://localhost:5000/Movies/Edit/2`) происходит следующее:

* Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`.
* Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*. Файл *Pages/Movies/Edit.cshtml* содержит директиву модели (`@model RazorPagesMovie.Pages.Movies.EditModel`), которая делает модель фильма доступной на странице.
* Отображается форма Edit со значениями из записи фильма.

При публикации страницы Movies/Edit происходит следующее:

* Значения формы на странице привязываются к свойству `Movie`. Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* При наличии ошибок в состоянии модели (например, `ReleaseDate` невозможно преобразовать в дату) форма отображается повторно с предоставленными значениями.
* Если ошибки модели отсутствуют, данные фильма сохраняются.

Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично. Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Приложение для работы с фильмами подготовлено, но представление данных далеко от идеала. Вместо **ReleaseDate** должно стоять **Дата выпуска** (два слова).

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Обновление созданного кода

Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки кода:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Заметка к данным `[Column(TypeName = "decimal(18, 2)")]` позволяет Entity Framework Core корректно сопоставить `Price` с валютой в базе данных. Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).

Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве. Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate"). Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.

Перейдите к Pages/Movies и наведите указатель мыши на ссылку **Edit** , чтобы увидеть конечный URL-адрес.

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Ссылки **Edit** , **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor. В приведенном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основе страницы Razor (маршрут является относительным), атрибут `asp-page` и идентификатор маршрута (`asp-route-id`). Дополнительные сведения см. в разделе [Формирование URL-адресов для страниц](xref:razor-pages/index#url-generation-for-pages).

Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере. Ниже показана часть созданного кода HTML:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

В динамически созданных ссылках идентификаторы фильмов передаются с помощью строки запроса (например, `?id=1` в `https://localhost:5001/Movies/Details?id=1`).

Обновите страницы Razor Edit, Details и Delete так, чтобы использовался шаблон маршрута "{id:int}". Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`. Запустите приложение и просмотрите исходный код. Созданный код HTML добавляет идентификатор в путь URL-адреса:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целое число, приводит к ошибке HTTP 404 (не найдено). Например, `http://localhost:5000/Movies/Details` приведет к ошибке 404. Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:

 ```cshtml
@page "{id:int?}"
```

Чтобы проверить поведение `@page "{id:int?}"`:

* Задайте директиву страницы *Pages/Movies/Details.cshtml* как `@page "{id:int?}"`.
* Установите точку останова `public async Task<IActionResult> OnGetAsync(int? id)` (в *Pages/Movies/Details.cshtml.cs* ).
* Перейдите к `https://localhost:5001/Movies/Details/`.

Из-за директивы `@page "{id:int}"` точка останова не достигается. Механизм маршрутизации возвращает ошибку HTTP 404. При использовании `@page "{id:int?}"` метод `OnGetAsync` возвращает `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Проверка обработки исключений нежесткой блокировки

Проверьте метод `OnPostAsync` в файле *Pages/Movies/Edit.cshtml.cs*.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Приведенный выше код обнаруживает исключения нежесткой блокировки, когда один клиент удаляет фильм, а другой публикует изменения для этого фильма.

Чтобы протестировать блок `catch`, выполните указанные ниже действия.

* Задайте точку останова в `catch (DbUpdateConcurrencyException)`
* Выберите команду **Изменить** у фильма, внесите изменения, но не вводите **Сохранить**.
* В другом окне браузера щелкните ссылку **Delete** для этого же фильма, а затем удалите его.
* В первом окне браузера опубликуйте изменения для фильма.

Коду в рабочей среде может потребоваться обнаружение конфликтов параллелизма. Дополнительные сведения см. в статье [Обработка конфликтов параллелизма](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Проверка публикации и привязки

Изучите файл *Pages/Movies/Edit.cshtml.cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

При выполнении HTTP-запроса GET к странице Movies/Edit (например, `http://localhost:5000/Movies/Edit/2`) происходит следующее:

* Метод `OnGetAsync` извлекает запись фильма из базы данных и возвращает метод `Page`. 
* Метод `Page` отображает страницу Razor *Pages/Movies/Edit.cshtml*. Файл *Pages/Movies/Edit.cshtml* содержит директиву модели (`@model RazorPagesMovie.Pages.Movies.EditModel`), которая делает модель фильма доступной на странице.
* Отображается форма Edit со значениями из записи фильма.

При публикации страницы Movies/Edit происходит следующее:

* Значения формы на странице привязываются к свойству `Movie`. Атрибут `[BindProperty]` обеспечивает [привязку модели](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* При наличии ошибок в состоянии модели (например, `ReleaseDate` невозможно преобразовать в дату) форма отображается с предоставленными значениями.
* Если ошибки модели отсутствуют, данные фильма сохраняются.

Методы HTTP GET на страницах Razor Index, Create и Delete работают аналогично. Метод HTTP POST `OnPostAsync` на странице Razor Create работает аналогично методу `OnPostAsync` на странице Razor Edit.

В следующем учебнике будет добавлена функция поиска.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Предыдущая статья. Работа с базой данных](xref:tutorials/razor-pages/sql)
> [Следующая статья. Добавление поиска](xref:tutorials/razor-pages/search)

::: moniker-end
