---
title: Часть 6. Методы и представления контроллера в ASP.NET Core
author: rick-anderson
description: Часть 6. Добавление модели в приложение MVC ASP.NET Core
ms.author: riande
ms.date: 12/13/2018
no-loc:
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 07b67cd7c267c39b99277114b73642b5caa3e312
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632841"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="00897-103">Часть 6. Методы и представления контроллера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00897-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="00897-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="00897-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="00897-105">Все готово для приложения по работе с фильмами, но презентация далеко не идеальна, например, элемент **ReleaseDate** должен состоять из двух слов.</span><span class="sxs-lookup"><span data-stu-id="00897-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Представление "Индекс": заголовок "Release Date" (Дата выпуска) состоит из одного слова (без пробела), и рядом с каждой датой отображается время "00:00"](working-with-sql/_static/m55.png)

<span data-ttu-id="00897-107">Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки:</span><span class="sxs-lookup"><span data-stu-id="00897-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="00897-108">Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="00897-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="00897-109">Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="00897-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="00897-110">Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.</span><span class="sxs-lookup"><span data-stu-id="00897-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="00897-111">Требуются заметки к данным `[Column(TypeName = "decimal(18, 2)")]`, чтобы Entity Framework Core корректно сопоставила `Price` с валютой в базе данных.</span><span class="sxs-lookup"><span data-stu-id="00897-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="00897-112">Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="00897-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="00897-113">Перейдите к контроллеру `Movies`. Наведите указатель мыши на ссылку **Edit** (Изменить) и удерживайте его на месте, чтобы просмотреть целевой URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="00897-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="00897-115">Ссылки **Edit** (Изменить), **Details** (Сведения) и **Delete** (Удалить) создаются вспомогательной функцией тегов привязки Core MVC в файле *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="00897-115">The **Edit**, **Details**, and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="00897-116">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor.</span><span class="sxs-lookup"><span data-stu-id="00897-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="00897-117">В представленном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основании метода действия контроллера и идентификатора маршрута. Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере или средства для разработчика.</span><span class="sxs-lookup"><span data-stu-id="00897-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="00897-118">Ниже показана часть созданного кода HTML:</span><span class="sxs-lookup"><span data-stu-id="00897-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="00897-119">Повторите вызов формата [routing](xref:mvc/controllers/routing), настроенный в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="00897-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="00897-120">ASP.NET Core преобразует `https://localhost:5001/Movies/Edit/4` в запрос метода действия `Edit` контроллера `Movies` с параметром `Id`, равным 4.</span><span class="sxs-lookup"><span data-stu-id="00897-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="00897-121">(Методы контроллера также называются методами действия.)</span><span class="sxs-lookup"><span data-stu-id="00897-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="00897-122">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) являются одной из самых популярных новых возможностей в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="00897-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="00897-123">Подробнее см. в разделе [Дополнительные ресурсы](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="00897-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="00897-124">Откройте контроллер `Movies` и изучите два метода действия `Edit`.</span><span class="sxs-lookup"><span data-stu-id="00897-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="00897-125">В следующем коде демонстрируется метод `HTTP GET Edit`, который выполняет выборку фильмов и заполняет форму редактирования, созданную файлом Razor *Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="00897-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* Razor file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="00897-126">В следующем коде показан метод `HTTP POST Edit`, который является владельцем переданных значений фильмов:</span><span class="sxs-lookup"><span data-stu-id="00897-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="00897-127">В следующем коде показан метод `HTTP POST Edit`, который является владельцем переданных значений фильмов:</span><span class="sxs-lookup"><span data-stu-id="00897-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="00897-128">Атрибут `[Bind]` является одним из способов защиты от [чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="00897-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="00897-129">Свойства необходимо включать только в тот атрибут `[Bind]`, который вы хотите изменить.</span><span class="sxs-lookup"><span data-stu-id="00897-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="00897-130">Дополнительные сведения см. в разделе [Защита контроллера от чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="00897-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="00897-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) реализует альтернативный подход к защите от чрезмерной передачи данных.</span><span class="sxs-lookup"><span data-stu-id="00897-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="00897-132">Обратите внимание на второй метод действия `Edit`, которому предшествует атрибут `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="00897-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="00897-133">Атрибут `HttpPost` указывает на то, что этот метод `Edit` может вызываться *только* для запросов `POST`.</span><span class="sxs-lookup"><span data-stu-id="00897-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="00897-134">Вы могли бы применить атрибут `[HttpGet]` к первому методу редактирования, однако это необязательно, поскольку значение `[HttpGet]` задается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="00897-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="00897-135">Атрибут `ValidateAntiForgeryToken` используется для [защиты от подделки запросов](xref:security/anti-request-forgery) и используется совместно с соответствующим маркером безопасности, который создается в файле представления редактирования (*Views/Movies/Edit.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="00897-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file (*Views/Movies/Edit.cshtml*).</span></span> <span data-ttu-id="00897-136">В файле представления редактирования для создания маркера защиты от подделки используется [вспомогательная функция тега Form](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="00897-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="00897-137">[Вспомогательная функция тега Form](xref:mvc/views/working-with-forms) создает скрытый маркер защиты от подделки, который должен соответствовать `[ValidateAntiForgeryToken]` аналогичному маркеру безопасности в методе `Edit` контроллера Movies.</span><span class="sxs-lookup"><span data-stu-id="00897-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="00897-138">Дополнительные сведения см. в разделе <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="00897-138">For more information, see <xref:security/anti-request-forgery>.</span></span>

<span data-ttu-id="00897-139">Метод `HttpGet Edit` принимает параметр фильма `ID`, выполняет поиск фильма с использованием метода `FindAsync` платформы Entity Framework и возвращает выбранный фильм в представление редактирования.</span><span class="sxs-lookup"><span data-stu-id="00897-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="00897-140">Если фильм найти не удается, возвращается ошибка `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="00897-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="00897-141">Если в представлении редактирования создана система формирования шаблонов, она проверяет класс `Movie` и создает код для отображения элементов `<label>` и `<input>` для каждого свойства класса.</span><span class="sxs-lookup"><span data-stu-id="00897-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="00897-142">В следующем примере показано представление редактирования, созданное системой формирования шаблонов Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="00897-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="00897-143">Обратите внимание, что в начале файла шаблона представления содержится оператор `@model MvcMovie.Models.Movie`.</span><span class="sxs-lookup"><span data-stu-id="00897-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="00897-144">`@model MvcMovie.Models.Movie` указывает, что в представлении требуется модель представления шаблона с типом `Movie`.</span><span class="sxs-lookup"><span data-stu-id="00897-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="00897-145">Для оптимизации разметки HTML сформированный код использует несколько методов вспомогательных функций тегов.</span><span class="sxs-lookup"><span data-stu-id="00897-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="00897-146">[Вспомогательная функция тега Label](xref:mvc/views/working-with-forms) отображает имя поля ("Title", "ReleaseDate", "Genre" или "Price").</span><span class="sxs-lookup"><span data-stu-id="00897-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="00897-147">[Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) отображает элемент HTML `<input>`.</span><span class="sxs-lookup"><span data-stu-id="00897-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="00897-148">[Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms) отображает любые сообщения проверки, связанные с указанным свойством.</span><span class="sxs-lookup"><span data-stu-id="00897-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="00897-149">Запустите приложение и перейдите по URL-адресу `/Movies`.</span><span class="sxs-lookup"><span data-stu-id="00897-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="00897-150">Щелкните ссылку **Edit** (Изменить).</span><span class="sxs-lookup"><span data-stu-id="00897-150">Click an **Edit** link.</span></span> <span data-ttu-id="00897-151">Просмотрите исходный код страницы в окне браузера.</span><span class="sxs-lookup"><span data-stu-id="00897-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="00897-152">Созданный HTML-код для элемента `<form>` показан ниже.</span><span class="sxs-lookup"><span data-stu-id="00897-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="00897-153">Элементы `<input>` находятся в элементе `HTML <form>`, атрибут `action` которого задает передачу данных по URL-адресу `/Movies/Edit/id`.</span><span class="sxs-lookup"><span data-stu-id="00897-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="00897-154">Данные формы будут передаваться на сервер при нажатии кнопки `Save`.</span><span class="sxs-lookup"><span data-stu-id="00897-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="00897-155">В последней строке перед закрывающим элементом `</form>` отображается скрытый маркер [XSRF](xref:security/anti-request-forgery), созданный [вспомогательной функцией тега Form](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="00897-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="00897-156">Обработка запроса POST</span><span class="sxs-lookup"><span data-stu-id="00897-156">Processing the POST Request</span></span>

<span data-ttu-id="00897-157">В следующем листинге демонстрируется версия `[HttpPost]` метода действия `Edit`.</span><span class="sxs-lookup"><span data-stu-id="00897-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="00897-158">Атрибут `[ValidateAntiForgeryToken]` проверяет скрытый маркер безопасности [XSRF](xref:security/anti-request-forgery), который был создан генератором маркеров во [вспомогательной функции тега Form](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="00897-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="00897-159">Система [модели привязки](xref:mvc/models/model-binding) принимает переданные значения формы и создает объект `Movie`, который передается в качестве параметра `movie`.</span><span class="sxs-lookup"><span data-stu-id="00897-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="00897-160">Метод `ModelState.IsValid` проверяет, можно ли использовать переданные в форме данные для изменения (редактирования или обновления) объекта `Movie`.</span><span class="sxs-lookup"><span data-stu-id="00897-160">The `ModelState.IsValid` method verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="00897-161">Допустимые данные сохраняются.</span><span class="sxs-lookup"><span data-stu-id="00897-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="00897-162">Обновленные (измененные) данные фильма сохраняются в базе данных посредством вызова метода `SaveChangesAsync` в контексте базы данных.</span><span class="sxs-lookup"><span data-stu-id="00897-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="00897-163">После сохранения данных код перенаправляет пользователя в метод действия `Index` класса `MoviesController`, который отображает коллекцию фильмов с учетом только что внесенных изменений.</span><span class="sxs-lookup"><span data-stu-id="00897-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="00897-164">Перед отправкой формы на сервер на стороне клиента проверяется выполнение всех правил проверки для полей.</span><span class="sxs-lookup"><span data-stu-id="00897-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="00897-165">При обнаружении ошибок проверки отображается сообщение об ошибке, а форма не передается.</span><span class="sxs-lookup"><span data-stu-id="00897-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="00897-166">Если JavaScript отключен, проверка на стороне клиента не выполняется. Тем не менее, сервер обнаружит переданные недопустимые значения, в результате чего значения формы будут отображены повторно с сообщениями об ошибках.</span><span class="sxs-lookup"><span data-stu-id="00897-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="00897-167">Далее в этом руководстве мы более подробно изучим [проверку модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="00897-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="00897-168">[Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms) в шаблоне представления *Views/Movies/Edit.cshtml* обеспечивает отображение соответствующих сообщений об ошибке.</span><span class="sxs-lookup"><span data-stu-id="00897-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Представление редактирования: исключение, связанное с некорректным значением Price для abc, указывает, что поле Price должно содержать числовое значение.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="00897-171">Все методы `HttpGet` в контроллере Movie имеют схожий шаблон.</span><span class="sxs-lookup"><span data-stu-id="00897-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="00897-172">Они получают объект фильма (или список объектов для метода `Index`) и передают объект (модель) в представление.</span><span class="sxs-lookup"><span data-stu-id="00897-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="00897-173">Метод `Create` передает в представление пустой объект фильма `Create`.</span><span class="sxs-lookup"><span data-stu-id="00897-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="00897-174">Все методы, которые создают, редактируют, удаляют или иным образом изменяют данные, делают это в перегрузке метода `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="00897-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="00897-175">Изменение данных в методе `HTTP GET` сопряжено с угрозой безопасности.</span><span class="sxs-lookup"><span data-stu-id="00897-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="00897-176">Изменение данных в методе `HTTP GET` также не соответствует рекомендациям по использованию протокола HTTP и модели архитектуры [REST](http://rest.elkstein.org/), в которых указывается, что запросы GET не должны изменять состояние приложения.</span><span class="sxs-lookup"><span data-stu-id="00897-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="00897-177">Другими словами, операция GET должна выполняться безопасным способом, то есть не иметь побочных эффектов и не изменять существующие данные.</span><span class="sxs-lookup"><span data-stu-id="00897-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00897-178">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="00897-178">Additional resources</span></span>

* [<span data-ttu-id="00897-179">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="00897-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="00897-180">Общие сведения о вспомогательных функциях тегов</span><span class="sxs-lookup"><span data-stu-id="00897-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="00897-181">Создание вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="00897-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* <span data-ttu-id="00897-182">Защита контроллера от [чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="00897-182">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="00897-183">ViewModels</span><span class="sxs-lookup"><span data-stu-id="00897-183">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="00897-184">Вспомогательная функция тега Form</span><span class="sxs-lookup"><span data-stu-id="00897-184">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="00897-185">Вспомогательная функция тега Input</span><span class="sxs-lookup"><span data-stu-id="00897-185">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="00897-186">Вспомогательная функция тега Label</span><span class="sxs-lookup"><span data-stu-id="00897-186">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="00897-187">Вспомогательная функция тега Select</span><span class="sxs-lookup"><span data-stu-id="00897-187">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="00897-188">Вспомогательная функция тега Validation</span><span class="sxs-lookup"><span data-stu-id="00897-188">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="00897-189">[Назад](working-with-sql.md)
> [Вперед](search.md)</span><span class="sxs-lookup"><span data-stu-id="00897-189">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
