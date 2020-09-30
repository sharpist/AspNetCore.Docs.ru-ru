---
title: Часть 6. Razor Pages с EF Core в ASP.NET Core — чтение связанных данных
author: rick-anderson
description: Часть 6 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: dd00b9d7faf467857ec1e47f4cfb0296d84e5d3f
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847706"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Часть 6. Razor Pages с EF Core в ASP.NET Core — чтение связанных данных

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Этот учебник посвящен чтению и отображению связанных данных. Связанными называются данные, которые EF Core загружает в свойства навигации.

На следующих рисунках изображены готовые страницы для этого руководства:

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Безотложная, явная и отложенная загрузка

Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:

* [Безотложная загрузка](/ef/core/querying/related-data#eager-loading). Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности. При чтении сущности извлекаются ее связанные данные. Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные. Для некоторых типов безотложной загрузки EF Core выдает несколько запросов. Отправка нескольких запросов может оказаться эффективнее, чем выполнение одного большого запроса. Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:

  * Один запрос для основного запроса 
  * Один запрос для каждого "края" коллекции в дереве загрузки.

* Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации. Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации. Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  **Примечание.** EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста. Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.

* [Явная загрузка](/ef/core/querying/related-data#explicit-loading). При первом чтении сущности связанные данные не извлекаются. Нужно написать код, извлекающий связанные данные, когда они необходимы. Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных. При явной загрузке код указывает, какие свойства навигации нужно загрузить. Для выполнения явной загрузки используется метод `Load`. Пример:

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* [Отложенная загрузка](/ef/core/querying/related-data#lazy-loading). При первом чтении сущности связанные данные не извлекаются. При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически. Запрос к базе данных отправляется при каждом первом обращении к свойству навигации. Отложенная загрузка может снизить производительность, например когда разработчики используют шаблоны N+1, загружая родительский объект и перечисляя дочерние объекты.

## <a name="create-course-pages"></a>Создание страниц курсов

Сущность `Course` включает в себя свойство навигации, содержащее связанную сущность `Department`.

![Course.Department](read-related-data/_static/dep-crs.png)

Чтобы отобразить имя кафедры, которой назначен курс, выполните указанные ниже действия.

* Загрузите связанную сущность `Department` в свойство навигации `Course.Department`.
* Получите имя из свойства `Name` сущности `Department`.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Формирование шаблона для страниц курсов

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:

  * Создайте папку *Pages/Courses*.
  * Используйте класс модели `Course`.
  * Используйте существующий класс контекста вместо создания нового.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Создайте папку *Pages/Courses*.

* Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц курсов.

  **В Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **В Linux или macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`. Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`. Метод `Include` задает безотложную загрузку.

* Запустите приложение и выберите ссылку **Courses** (Курсы). В столбце кафедры отображается бесполезный `DepartmentID`.

### <a name="display-the-department-name"></a>Отображение названия кафедры

Измените файл Pages/Courses/Index.cshtml.cs, используя следующий код:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Приведенный выше код изменяет свойство `Course` на `Courses` и добавляет `AsNoTracking`. `AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются. Отслеживать сущности не нужно, так как они не изменяются в текущем контексте.

Измените файл *Pages/Courses/Index.cshtml*, используя приведенный ниже код.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

В шаблонный код были внесены следующие изменения:

* Имя свойства `Course` изменилось на `Courses`.
* Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`. По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла. Однако в нашем случае первичный ключ имеет смысл.
* Изменен столбец **Department** (Кафедра) для отображения названия кафедры. Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Загрузка связанных данных с помощью "Select"

Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`. Метод `Select` является альтернативным вариантом, который загружает только необходимые связанные данные. Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN. Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.

Следующий код загружает связанные данные с помощью метода `Select`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Приведенный выше код не возвращает никаких типов сущностей, поэтому отслеживание не выполняется. Дополнительные сведения об отслеживании EF см. в разделе [Отслеживание или Отключение отслеживания запросов](/ef/core/querying/tracking).

`CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-instructor-pages"></a>Создание страниц преподавателей

В этом разделе формируется шаблон для страниц преподавателей, а затем на страницу указателя преподавателей добавляются связанные курсы и регистрации.

<a name="IP"></a>
![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)

Эта страница считывает и отображает связанные данные следующим образом:

* Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении). Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному. Безотложная загрузка используется для сущностей `OfficeAssignment`. Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные. В этом случае отображается принадлежность к кабинету для каждого преподавателя.
* Когда пользователь выбирает преподавателя, отображаются связанные сущности `Course`. Между сущностями `Instructor` и `Course` действует связь многие ко многим. Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`. В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя. Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.
* Когда пользователь выбирает курс, отображаются связанные данные из сущности `Enrollments`. На предыдущем изображении отображается имя и оценка учащегося. Между сущностями `Course` и `Enrollment` действует связь один ко многим.

### <a name="create-a-view-model"></a>Создание модели представления

На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц. Требуется модель представления, которая включает в себя три свойства, представляющие три таблицы.

Создайте файл *SchoolViewModels/InstructorIndexData.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Формирование шаблона для страниц преподавателей

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:

  * Создайте папку *Pages/Instructors*.
  * Используйте класс модели `Instructor`.
  * Используйте существующий класс контекста вместо создания нового.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Создайте папку *Pages/Instructors*.

* Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц преподавателей.

  **В Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **В Linux или macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Чтобы увидеть, как выглядит шаблонная страница до ее изменения, запустите приложение и перейдите на страницу преподавателей.

Измените файл *Pages/Instructors/Index.cshtml.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.

Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

В коде задается безотложная загрузка для следующих свойств навигации:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Обратите внимание на то, что методы `Include` и `ThenInclude` повторяются для `CourseAssignments` и `Course`. Это необходимо для того, чтобы задать безотложную загрузку для двух свойств навигации сущности `Course`.

Приведенный ниже код выполняется при выборе преподавателя (`id != null`).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Выбранный преподаватель извлекается из списка преподавателей в модели представления. Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.

Метод `Where` возвращает коллекцию. Но в этом случае фильтр выберет одну сущность, поэтому вызывается метод `Single` для преобразования коллекции в одну сущность `Instructor`. Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`. `CourseAssignments` предоставляет доступ к связанным сущностям `Course`.

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

Метод `Single` используется для коллекции, когда она содержит всего один элемент. Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента. Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.

Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Изменение страницы индекса преподавателей

Измените файл *Pages/Instructors/Index.cshtml*, используя следующий код:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Приведенный выше код вносит следующие изменения:

* Изменяет директиву `page` с `@page` на `@page "{id:int?}"`. `"{id:int?}"` является шаблоном маршрута. Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных. Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:

  `https://localhost:5001/Instructors?id=2`

  Когда используется директива страницы `@page "{id:int?}"`, URL-адрес имеет следующее значение:

  `https://localhost:5001/Instructors/2`

* Добавляет столбец **Office**, в котором отображается `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно NULL. Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Добавляет столбец **Courses**, содержащий курсы, которые ведет конкретный преподаватель. Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).

* Добавляет код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя и курса. Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Добавляет новую гиперссылку с меткой **Select** (Выбрать). Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Добавляет таблицу курсов для выбранного преподавателя.

* Добавляет таблицу регистраций учащихся для выбранного курса.

Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`. Если `OfficeAssignment` имеет значение NULL, отображается пустая ячейка таблицы.

Щелкните ссылку **Select** для преподавателя. Стиль строки изменится, и отобразятся курсы, назначенные этому преподавателю.

Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Использование метода Single

Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Использование `Single` с условием WHERE — это вопрос личных предпочтений. Оно не дает преимуществ по сравнению с использованием метода `Where`.

## <a name="explicit-loading"></a>Явная загрузка

Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Предположим, что пользователям редко требуется просматривать зачисления на курс. В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе. В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.

Измените файл *Pages/Instructors/Index.cshtml.cs*, используя приведенный ниже код.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся. Если курс выбран, код явной загрузки извлекает следующее:

* Сущности `Enrollment` для выбранного курса.
* Сущности `Student` для каждого `Enrollment`.

Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`. Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.

Проверьте работу приложения. С точки зрения пользователя приложение работает аналогично предыдущей версии.

## <a name="next-steps"></a>Следующие шаги

Следующее руководство посвящено обновлению связанных данных.

>[!div class="step-by-step"]
>[Предыдущий учебник](xref:data/ef-rp/complex-data-model)
>[Следующий учебник](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом руководстве выполняется чтение и отображение связанных данных. Связанными называются данные, которые EF Core загружает в свойства навигации.

При возникновении проблем, которые вам не удается устранить, [скачайте или просмотрите готовое приложение.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Указания по скачиванию](xref:index#how-to-download-a-sample).

На следующих рисунках изображены готовые страницы для этого руководства:

![Страница индекса курсов](read-related-data/_static/courses-index.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Безотложная, явная и отложенная загрузка связанных данных

Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:

* [Безотложная загрузка](/ef/core/querying/related-data#eager-loading). Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности. При чтении сущности связанные данные извлекаются. Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные. Для некоторых типов безотложной загрузки EF Core выдает несколько запросов. Выдача нескольких запросов может оказаться более эффективной по сравнению с отдельными ситуациями в EF6, когда выполнялся отдельный запрос. Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:

  * Один запрос для основного запроса 
  * Один запрос для каждого "края" коллекции в дереве загрузки.

* Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации. Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации. Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  Примечание. EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста. Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.

* [Явная загрузка](/ef/core/querying/related-data#explicit-loading). При первом чтении сущности связанные данные не извлекаются. Нужно написать код, извлекающий связанные данные, когда они необходимы. Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных. При явной загрузке код указывает, какие свойства навигации нужно загрузить. Для выполнения явной загрузки используется метод `Load`. Пример:

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* [Отложенная загрузка](/ef/core/querying/related-data#lazy-loading). [В EF Core версии 2.1 добавлена отложенная загрузка](/ef/core/querying/related-data#lazy-loading). При первом чтении сущности связанные данные не извлекаются. При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически. Запрос к базе данных отправляется при каждом первом обращении к свойству навигации.

* Оператор `Select` загружает только необходимые связанные данные.

## <a name="create-a-course-page-that-displays-department-name"></a>Создание страницы "Course" (Курс) с отображением названий кафедр

Сущность "Course" включает в себя свойство навигации, содержащее сущность `Department`. Сущность `Department` содержит кафедру, которой назначен курс.

Отображение имени назначенной кафедры в списке курсов:

* Получите свойство `Name` из сущности `Department`.
* Сущность `Department` получается из свойства навигации `Course.Department`.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Формирование шаблона для модели Course

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Course` для класса модели.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Предыдущая команда формирует шаблон для модели `Course`. Откройте проект в Visual Studio.

Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`. Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`. Метод `Include` задает безотложную загрузку.

Запустите приложение и выберите ссылку **Courses** (Курсы). В столбце кафедры отображается бесполезный `DepartmentID`.

Обновите метод `OnGetAsync`, используя следующий код:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Предыдущий код добавляет `AsNoTracking`. `AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются. Отслеживание отсутствует, так как сущности не изменяются в текущем контексте.

Измените *Pages/Courses/Index.cshtml*, используя выделенную ниже разметку:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

В шаблонный код были внесены следующие изменения:

* Изменен заголовок с "Index" (Индекс) на "Courses" (Курсы).
* Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`. По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла. Однако в нашем случае первичный ключ имеет смысл.
* Изменен столбец **Department** (Кафедра) для отображения названия кафедры. Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).

![Страница индекса курсов](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Загрузка связанных данных с помощью "Select"

Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Оператор `Select` загружает только необходимые связанные данные. Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN. Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.

Следующий код загружает связанные данные с помощью метода `Select`:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Создание страницы "Instructors" (Преподаватели) с отображением курсов и дат зачисления

В этом разделе создается страница "Instructors" (Преподаватели).

<a name="IP"></a>
![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

Эта страница считывает и отображает связанные данные следующим образом:

* Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении). Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному. Безотложная загрузка используется для сущностей `OfficeAssignment`. Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные. В этом случае отображается принадлежность к кабинету для каждого преподавателя.
* Когда пользователь выбирает преподавателя (Harui на предыдущем изображении), отображаются связанные сущности `Course`. Между сущностями `Instructor` и `Course` действует связь многие ко многим. Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`. В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя. Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.
* Когда пользователь выбирает курс ("Chemistry" (Химия) на предыдущем изображении), отображаются связанные данные из сущности `Enrollments`. На предыдущем изображении отображается имя и оценка учащегося. Между сущностями `Course` и `Enrollment` действует связь один ко многим.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Создание модели для представления индекса преподавателей

На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц. Создается модель представления, которая включает три сущности, представляющие три таблицы.

Создайте в папке *SchoolViewModels* файл *InstructorIndexData.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Формирование шаблона для модели "Instructor" (Преподаватель)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Instructor` для класса модели.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Предыдущая команда формирует шаблон для модели `Instructor`. 
Запустите приложение и перейдите на страницу преподавателей.

Замените содержимое *Pages/Instructors/Index.cshtml.cs* на следующий код:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.

Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Запрос имеет две операции включения:

* `OfficeAssignment`. отображается в [представлении преподавателей](#IP).
* `CourseAssignments`. вызывает проводимые курсы.

### <a name="update-the-instructors-index-page"></a>Изменение страницы индекса преподавателей

Измените *Pages/Instructors/Index.cshtml*, используя следующую разметку:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Приведенная выше разметка вносит следующие изменения:

* Изменяет директиву `page` с `@page` на `@page "{id:int?}"`. `"{id:int?}"` является шаблоном маршрута. Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных. Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:

  `http://localhost:1234/Instructors?id=2`

  Когда используется директива страницы `@page "{id:int?}"`, предыдущий URL-адрес имеет следующее значение:

  `http://localhost:1234/Instructors/2`

* Заголовком страницы является **Instructors**.
* Добавили столбец **Office**, отображающий `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно null. Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Добавили столбец **Courses**, отображающий курсы, которые ведет конкретный преподаватель. Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).

* Добавили код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя. Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Добавлена новая гиперссылка с меткой **Select** (Выбрать). Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`. Если OfficeAssignment имеет значение null, отображается пустая ячейка таблицы.

Щелкните ссылку **Select** (Выбрать). Изменяется стиль строки.

### <a name="add-courses-taught-by-selected-instructor"></a>Добавление курсов, проводимых выбранным преподавателем

Измените метод `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Добавление `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Проверьте измененный запрос:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Предыдущий запрос добавляет сущности `Department`.

Приведенный ниже код выполняется при выборе преподавателя (`id != null`). Выбранный преподаватель извлекается из списка преподавателей в модели представления. Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Метод `Where` возвращает коллекцию. В предыдущем методе `Where` возвращается всего одна сущность `Instructor`. Метод `Single` преобразует коллекцию в отдельную сущность `Instructor`. Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`. `CourseAssignments` предоставляет доступ к связанным сущностям `Course`.

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

Метод `Single` используется для коллекции, когда она содержит всего один элемент. Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента. Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста. Использование `SingleOrDefault` для пустой коллекции:

* Возникает исключение (из-за попытки найти свойство `Courses` по пустой ссылке).
* Сообщение об исключении не так четко указывает на причину проблемы.

Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Добавьте следующую разметку в конец страницы Razor *Pages/Instructors/Index.cshtml*.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Предыдущая разметка отображает список связанных с преподавателем курсов при выборе преподавателя.

Проверьте работу приложения. Щелкните ссылку **Select** (Выбрать) на странице преподавателей.

### <a name="show-student-data"></a>Отображение данных об учащихся

В этом разделе приложение изменяется, чтобы отобразить данные об учащихся для выбранного курса.

Измените запрос в методе `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Измените *Pages/Instructors/Index.cshtml*. Добавьте следующую разметку в конец файла:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Предыдущая разметка отображает список учащихся, которые зачислены на курс.

Обновите страницу и выберите преподавателя. Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Использование метода Single

Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Приведенный выше подход на основе `Single` не дает преимуществ по сравнению с использованием `Where`. Некоторые разработчики предпочитают использовать подход `Single`.

## <a name="explicit-loading"></a>Явная загрузка

Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Предположим, что пользователям редко требуется просматривать зачисления на курс. В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе. В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.

Измените `OnGetAsync`, используя следующий код:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся. Если курс выбран, выделенный код извлекает следующее:

* Сущности `Enrollment` для выбранного курса.
* Сущности `Student` для каждого `Enrollment`.

Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`. Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.

Проверьте работу приложения. С точки зрения пользователей приложение работает аналогично предыдущей версии.

Следующее руководство посвящено обновлению связанных данных.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube (часть 1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Версия руководства на YouTube (часть 2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Назад](xref:data/ef-rp/complex-data-model)
>[Вперед](xref:data/ef-rp/update-related-data)

::: moniker-end
