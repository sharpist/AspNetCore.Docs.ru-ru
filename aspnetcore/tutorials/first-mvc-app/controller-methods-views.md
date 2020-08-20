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
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>Часть 6. Методы и представления контроллера в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Все готово для приложения по работе с фильмами, но презентация далеко не идеальна, например, элемент **ReleaseDate** должен состоять из двух слов.

![Представление "Индекс": заголовок "Release Date" (Дата выпуска) состоит из одного слова (без пробела), и рядом с каждой датой отображается время "00:00"](working-with-sql/_static/m55.png)

Откройте файл *Models/Movie.cs* и добавьте указанные ниже выделенные строки:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Пространство имен [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) будет рассмотрено в следующем руководстве. Атрибут [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) определяет отображаемое имя поля (в этом случае "Release Date" вместо "ReleaseDate"). Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) определяет тип данных (Date), поэтому сведения о времени, хранящиеся в поле, не отображаются.

Требуются заметки к данным `[Column(TypeName = "decimal(18, 2)")]`, чтобы Entity Framework Core корректно сопоставила `Price` с валютой в базе данных. Дополнительные сведения см. в разделе [Типы данных](/ef/core/modeling/relational/data-types).

Перейдите к контроллеру `Movies`. Наведите указатель мыши на ссылку **Edit** (Изменить) и удерживайте его на месте, чтобы просмотреть целевой URL-адрес.

![Окно браузера с указателем, наведенным на ссылку Edit (Изменить), и URL-адресом ссылки https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Ссылки **Edit** (Изменить), **Details** (Сведения) и **Delete** (Удалить) создаются вспомогательной функцией тегов привязки Core MVC в файле *Views/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) позволяют серверному коду участвовать в создании и отображении HTML-элементов в файлах Razor. В представленном выше коде `AnchorTagHelper` динамически создает значение атрибута HTML `href` на основании метода действия контроллера и идентификатора маршрута. Для изучения созданной разметки используйте функцию **просмотра исходного кода** в вашем любимом браузере или средства для разработчика. Ниже показана часть созданного кода HTML:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Повторите вызов формата [routing](xref:mvc/controllers/routing), настроенный в файле *Startup.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core преобразует `https://localhost:5001/Movies/Edit/4` в запрос метода действия `Edit` контроллера `Movies` с параметром `Id`, равным 4. (Методы контроллера также называются методами действия.)

[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) являются одной из самых популярных новых возможностей в ASP.NET Core. Подробнее см. в разделе [Дополнительные ресурсы](#additional-resources).

<a name="get-post"></a>

Откройте контроллер `Movies` и изучите два метода действия `Edit`. В следующем коде демонстрируется метод `HTTP GET Edit`, который выполняет выборку фильмов и заполняет форму редактирования, созданную файлом Razor *Edit.cshtml*.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

В следующем коде показан метод `HTTP POST Edit`, который является владельцем переданных значений фильмов:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

В следующем коде показан метод `HTTP POST Edit`, который является владельцем переданных значений фильмов:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Атрибут `[Bind]` является одним из способов защиты от [чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Свойства необходимо включать только в тот атрибут `[Bind]`, который вы хотите изменить. Дополнительные сведения см. в разделе [Защита контроллера от чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) реализует альтернативный подход к защите от чрезмерной передачи данных.

Обратите внимание на второй метод действия `Edit`, которому предшествует атрибут `[HttpPost]`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

Атрибут `HttpPost` указывает на то, что этот метод `Edit` может вызываться *только* для запросов `POST`. Вы могли бы применить атрибут `[HttpGet]` к первому методу редактирования, однако это необязательно, поскольку значение `[HttpGet]` задается по умолчанию.

Атрибут `ValidateAntiForgeryToken` используется для [защиты от подделки запросов](xref:security/anti-request-forgery) и используется совместно с соответствующим маркером безопасности, который создается в файле представления редактирования (*Views/Movies/Edit.cshtml*). В файле представления редактирования для создания маркера защиты от подделки используется [вспомогательная функция тега Form](xref:mvc/views/working-with-forms).

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Вспомогательная функция тега Form](xref:mvc/views/working-with-forms) создает скрытый маркер защиты от подделки, который должен соответствовать `[ValidateAntiForgeryToken]` аналогичному маркеру безопасности в методе `Edit` контроллера Movies. Дополнительные сведения см. в разделе <xref:security/anti-request-forgery>.

Метод `HttpGet Edit` принимает параметр фильма `ID`, выполняет поиск фильма с использованием метода `FindAsync` платформы Entity Framework и возвращает выбранный фильм в представление редактирования. Если фильм найти не удается, возвращается ошибка `NotFound` (HTTP 404).

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Если в представлении редактирования создана система формирования шаблонов, она проверяет класс `Movie` и создает код для отображения элементов `<label>` и `<input>` для каждого свойства класса. В следующем примере показано представление редактирования, созданное системой формирования шаблонов Visual Studio:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Обратите внимание, что в начале файла шаблона представления содержится оператор `@model MvcMovie.Models.Movie`. `@model MvcMovie.Models.Movie` указывает, что в представлении требуется модель представления шаблона с типом `Movie`.

Для оптимизации разметки HTML сформированный код использует несколько методов вспомогательных функций тегов. [Вспомогательная функция тега Label](xref:mvc/views/working-with-forms) отображает имя поля ("Title", "ReleaseDate", "Genre" или "Price"). [Вспомогательная функция тега Input](xref:mvc/views/working-with-forms) отображает элемент HTML `<input>`. [Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms) отображает любые сообщения проверки, связанные с указанным свойством.

Запустите приложение и перейдите по URL-адресу `/Movies`. Щелкните ссылку **Edit** (Изменить). Просмотрите исходный код страницы в окне браузера. Созданный HTML-код для элемента `<form>` показан ниже.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Элементы `<input>` находятся в элементе `HTML <form>`, атрибут `action` которого задает передачу данных по URL-адресу `/Movies/Edit/id`. Данные формы будут передаваться на сервер при нажатии кнопки `Save`. В последней строке перед закрывающим элементом `</form>` отображается скрытый маркер [XSRF](xref:security/anti-request-forgery), созданный [вспомогательной функцией тега Form](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Обработка запроса POST

В следующем листинге демонстрируется версия `[HttpPost]` метода действия `Edit`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Атрибут `[ValidateAntiForgeryToken]` проверяет скрытый маркер безопасности [XSRF](xref:security/anti-request-forgery), который был создан генератором маркеров во [вспомогательной функции тега Form](xref:mvc/views/working-with-forms)

Система [модели привязки](xref:mvc/models/model-binding) принимает переданные значения формы и создает объект `Movie`, который передается в качестве параметра `movie`. Метод `ModelState.IsValid` проверяет, можно ли использовать переданные в форме данные для изменения (редактирования или обновления) объекта `Movie`. Допустимые данные сохраняются. Обновленные (измененные) данные фильма сохраняются в базе данных посредством вызова метода `SaveChangesAsync` в контексте базы данных. После сохранения данных код перенаправляет пользователя в метод действия `Index` класса `MoviesController`, который отображает коллекцию фильмов с учетом только что внесенных изменений.

Перед отправкой формы на сервер на стороне клиента проверяется выполнение всех правил проверки для полей. При обнаружении ошибок проверки отображается сообщение об ошибке, а форма не передается. Если JavaScript отключен, проверка на стороне клиента не выполняется. Тем не менее, сервер обнаружит переданные недопустимые значения, в результате чего значения формы будут отображены повторно с сообщениями об ошибках. Далее в этом руководстве мы более подробно изучим [проверку модели](xref:mvc/models/validation). [Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms) в шаблоне представления *Views/Movies/Edit.cshtml* обеспечивает отображение соответствующих сообщений об ошибке.

![Представление редактирования: исключение, связанное с некорректным значением Price для abc, указывает, что поле Price должно содержать числовое значение. Исключение, связанное с некорректным значением "Release Date" для xyz, указывает на необходимость ввести допустимую дату.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Все методы `HttpGet` в контроллере Movie имеют схожий шаблон. Они получают объект фильма (или список объектов для метода `Index`) и передают объект (модель) в представление. Метод `Create` передает в представление пустой объект фильма `Create`. Все методы, которые создают, редактируют, удаляют или иным образом изменяют данные, делают это в перегрузке метода `[HttpPost]`. Изменение данных в методе `HTTP GET` сопряжено с угрозой безопасности. Изменение данных в методе `HTTP GET` также не соответствует рекомендациям по использованию протокола HTTP и модели архитектуры [REST](http://rest.elkstein.org/), в которых указывается, что запросы GET не должны изменять состояние приложения. Другими словами, операция GET должна выполняться безопасным способом, то есть не иметь побочных эффектов и не изменять существующие данные.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Глобализация и локализация](xref:fundamentals/localization)
* [Общие сведения о вспомогательных функциях тегов](xref:mvc/views/tag-helpers/intro)
* [Создание вспомогательных функций тегов](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* Защита контроллера от [чрезмерной передачи данных](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Вспомогательная функция тега Form](xref:mvc/views/working-with-forms)
* [Вспомогательная функция тега Input](xref:mvc/views/working-with-forms)
* [Вспомогательная функция тега Label](xref:mvc/views/working-with-forms)
* [Вспомогательная функция тега Select](xref:mvc/views/working-with-forms)
* [Вспомогательная функция тега Validation](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Назад](working-with-sql.md)
> [Вперед](search.md)  
