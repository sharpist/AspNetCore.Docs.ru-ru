---
title: Часть 2. Razor Pages с EF Core в ASP.NET Core — CRUD
author: rick-anderson
description: Часть 2 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/crud
ms.openlocfilehash: 083214c01dbec6c6f44d6b82f5b514a029e57cbe
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606736"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>Часть 2. Razor Pages с EF Core в ASP.NET Core — CRUD

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

В этом учебнике описывается проверка и настройка шаблонного кода операций CRUD (создание, чтение, обновление и удаление).

## <a name="no-repository"></a>Репозиторий отсутствует.

Некоторые разработчики используют уровень служб или шаблон репозитория, чтобы создать уровень абстракции между пользовательским интерфейсом (Razor Pages) и уровнем доступа к данным. В данном учебнике этого не делается. Чтобы максимально упростить код и сконцентрироваться на работе с EF Core, код EF Core добавляется непосредственно в классы моделей страниц. 

## <a name="update-the-details-page"></a>Обновление страницы Details (сведения)

Шаблонный код для страниц учащихся не включает в себя сведения о регистрации. В этом разделе вы добавите регистрации на страницу сведений.

### <a name="read-enrollments"></a>Считывание сведений о регистрации

Чтобы отобразить сведения о регистрации учащегося на странице, необходимо считать их. Шаблонный код в файле *Pages/Students/Details.cshtml.cs* считывает только сведения об учащихся, но не сведения о регистрации:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Замените метод `OnGetAsync` приведенным ниже кодом для считывания сведений о регистрации для выбранного учащегося. Изменения выделены.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Методы [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) и [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) инструктируют контекст для загрузки свойства навигации `Student.Enrollments`, а также свойства навигации `Enrollment.Course` в пределах каждой регистрации. Эти методы более подробно рассматриваются в учебнике, посвященном [чтению связанных данных](xref:data/ef-rp/read-related-data).

Метод [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) повышает производительность в тех сценариях, где возвращаемые сущности не обновляются в текущем контексте. `AsNoTracking` рассматривается позднее в этом учебнике.

### <a name="display-enrollments"></a>Отображение регистраций

Замените код в файле *Pages/Students/Details.cshtml* приведенным ниже кодом для отображения списка регистраций. Изменения выделены.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Приведенный выше код циклически обрабатывает сущности в свойстве навигации `Enrollments`. Для каждой регистрации он отображает название курса и оценку. Название курса извлекается из сущности Course, которая хранится в свойстве навигации `Course` сущности Enrollments.

Запустите приложение, выберите вкладку **Students** (Учащиеся) и щелкните ссылку **Details** (Сведения) для учащегося. Отобразится список курсов и оценок для выбранного учащегося.

### <a name="ways-to-read-one-entity"></a>Способы чтения одной сущности

В созданном коде для чтения одной сущности используется метод [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_). Этот метод возвращает значение NULL, если ничего не найдено. В противном случае возвращается первая найденная строка, удовлетворяющая условиям фильтра запросов. `FirstOrDefaultAsync` обычно предпочтительнее, чем следующие варианты:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) — вызывает исключение при наличии нескольких сущностей, соответствующих фильтру запросов. Для определения того, может ли запрос вернуть более одной строки, `SingleOrDefaultAsync` пытается получить несколько строк. Это дополнительное действие не требуется, если запрос может вернуть только одну сущность, как при поиске по уникальному ключу.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) — находит сущность с первичным ключом. Если сущность с первичным ключом отслеживается контекстом, она возвращается без запроса к базе данных. Этот метод оптимизирован для поиска одной сущности, однако вызвать `Include` с `FindAsync` невозможно.  Поэтому если требуются связанные данные, лучше использовать `FirstOrDefaultAsync`.

### <a name="route-data-vs-query-string"></a>Данные маршрута или строка запроса

URL-адрес страницы сведений — `https://localhost:<port>/Students/Details?id=1`. Значение первичного ключа сущности содержится в строке запроса. Некоторые разработчики предпочитают передавать значение ключа в данных маршрута: `https://localhost:<port>/Students/Details/1`. Дополнительные сведения см. в разделе [Обновление созданного кода](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Обновление страницы Create

Шаблонный код `OnPostAsync` для страницы создания уязвим к [чрезмерной передаче данных](#overposting). Замените метод `OnPostAsync` в файле *Pages/Students/Create.cshtml.cs*, используя следующий код:

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

В приведенном выше коде создается объект Student, после чего опубликованные поля формы используются для обновления свойств этого объекта. Метод [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) выполняет указанные ниже действия.

* Использует опубликованные значения формы из свойства [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) в [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Обновляет только перечисленные свойства (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* Ищет поля формы с префиксом "student". Например, `Student.FirstMidName`. Задается без учета регистра символов.
* Использует систему [привязки модели](xref:mvc/models/model-binding) для преобразования значений формы из строк в типы модели `Student`. Например, `EnrollmentDate` преобразуется в `DateTime`.

Запустите приложение и создайте сущность учащегося для тестирования страницы создания.

## <a name="overposting"></a>Чрезмерная передача данных

В целях повышения безопасности рекомендуется использовать `TryUpdateModel` для обновления полей на основе отправленных значений, поскольку в этом случае исключается чрезмерная передача данных. Например, сущность Student включает свойство `Secret`, которое веб-страница не должна обновлять или добавлять:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Даже если приложение не имеет поля `Secret` на странице создания или обновления Razor, злоумышленник может установить значение `Secret` посредством чрезмерной передачи данных. Злоумышленник может использовать такие средства, как Fiddler, или собственный код JavaScript для отправки значения формы `Secret`. В исходном коде не ограничиваются поля, которые используются при создании экземпляра Student связывателем модели.

Какое бы значение ни задал злоумышленник для поля формы `Secret`, оно будет обновлено в базе данных. На следующем рисунке показано средство Fiddler, с помощью которого в отправленные значения формы добавляется поле `Secret` со значением "OverPost".

![Добавление поля Secret с помощью средства Fiddler](../ef-mvc/crud/_static/fiddler.png)

Значение "OverPost" успешно добавлено в свойство `Secret` вставленной строки. Это происходит несмотря на то, что разработчик приложения не планировал, что свойство `Secret` будет устанавливаться на странице создания.

### <a name="view-model"></a>Модель представления

Модели представления реализуют альтернативный подход к защите от чрезмерной передачи данных.

Модель приложения часто называют моделью домена. Модель предметной области обычно содержит все свойства, необходимые для соответствующей сущности в базе данных. Модель представления содержит только те свойства, которые необходимы для страницы пользовательского интерфейса, например страницы Create.

Помимо модели представления в некоторых приложениях используется модель привязки или модель ввода для передачи данных между классом модели страницы Razor Pages и браузером. 

Рассмотрим следующую модель представления `StudentVM`:

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

В следующем коде модель представления используется `StudentVM` для создания нового учащегося:

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

Метод [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) устанавливает значения этого объекта, считывая значения из другого объекта [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` использует сопоставление имен свойств. Тип модели представления:

* не обязательно должен быть связан с типом модели;
* должен иметь соответствующие свойства.

При использовании `StudentVM` требуется, чтобы страница Create использовала `StudentVM`, а не `Student`:

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a>Обновление страницы редактирования

В файле *Pages/Students/Edit.cshtml.cs* замените методы `OnGetAsync` и `OnPostAsync` приведенным ниже кодом.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Изменения в коде аналогичны странице Create за некоторыми исключениями:

* `FirstOrDefaultAsync` заменен на [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Если включать связанные данные не требуется, более эффективным будет метод `FindAsync`.
* `OnPostAsync` имеет параметр `id`.
* Текущий учащийся извлекается из базы данных вместо того, чтобы создавать нового учащегося.

Запустите приложение и протестируйте его, создав и изменив учащегося.

## <a name="entity-states"></a>Состояния сущностей

Контекст базы данных отслеживает синхронизацию сущностей в памяти с соответствующими им строками в базе данных. Данные отслеживания определяют поведение при вызове метода [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_). Например, при передаче новой сущности в метод [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) ей присваивается состояние [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). При вызове метода `SaveChangesAsync` контекст базы данных выполняет команду SQL `INSERT`.

Возможны следующие [состояния сущности](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`. Сущность еще не существует в базе данных. Метод `SaveChanges` выполняет инструкцию `INSERT`.

* `Unchanged`. изменения сущности не сохраняются. Сущность находится в этом состоянии при считывании из базы данных.

* `Modified`. Были изменены значения некоторых или всех свойств сущности. Метод `SaveChanges` выполняет инструкцию `UPDATE`.

* `Deleted`. Сущность отмечена для удаления. Метод `SaveChanges` выполняет инструкцию `DELETE`.

* `Detached`. Сущность не отслеживается контекстом базы данных.

В классическом приложении изменения состояния обычно осуществляются автоматически. После считывания сущности и ее изменения ей автоматически присваивается состояние `Modified`. При вызове метода `SaveChanges` создается инструкция SQL `UPDATE`, которая обновляет только измененные свойства.

В веб-приложении объект `DbContext`, который считывает сущность и отображает ее данные, ликвидируется после отрисовки страницы. При вызове метода страницы `OnPostAsync` выполняется новый веб-запрос с новым экземпляром `DbContext`. Если повторно считать сущность в этот новый контекст, будет смоделирована обработка в классическом приложении.

## <a name="update-the-delete-page"></a>Обновление страницы удаления

В этом разделе реализуется пользовательское сообщение об ошибке на случай сбоя при вызове `SaveChanges`.

Замените код в файле *Pages/Students/Delete.cshtml.cs* на приведенный ниже. Изменения выделены:

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

В предыдущем коде в сигнатуру метода `OnGetAsync` добавляется необязательный параметр `saveChangesError`. `saveChangesError` указывает, был ли метод вызван после того, как произошел сбой при удалении объекта учащегося. Операция удаления может завершиться сбоем из-за временных проблем с сетью. Вероятность возникновения временных проблем с сетью выше, когда база данных размещается в облаке. Параметр `saveChangesError` имеет значение `false` при вызове метода `OnGetAsync` страницы удаления из пользовательского интерфейса. Если `OnGetAsync` вызывается методом `OnPostAsync` из-за сбоя операции удаления, параметру `saveChangesError` присваивается значение `true`.

Метод `OnPostAsync` извлекает выбранную сущность и вызывает метод [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_), чтобы присвоить ей состояние `Deleted`. При вызове метода `SaveChanges` создается инструкция SQL `DELETE`. В случае сбоя `Remove`:

* Возникает исключение базы данных.
* Вызывается метод `OnGetAsync` страницы Delete с параметром `saveChangesError=true`.

Добавьте сообщение об ошибке в файл *Pages/Students/Delete.cshtml*:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Запустите приложение и удалите учащегося, чтобы протестировать страницу удаления.

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Предыдущий учебник](xref:data/ef-rp/intro)
> [Следующий учебник](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

В этом учебнике описывается проверка и настройка шаблонного кода операций CRUD (создание, чтение, обновление и удаление).

## <a name="no-repository"></a>Репозиторий отсутствует.

Некоторые разработчики используют уровень служб или шаблон репозитория, чтобы создать уровень абстракции между пользовательским интерфейсом (Razor Pages) и уровнем доступа к данным. В данном учебнике этого не делается. Чтобы максимально упростить код и сконцентрироваться на работе с EF Core, код EF Core добавляется непосредственно в классы моделей страниц. 

## <a name="update-the-details-page"></a>Обновление страницы Details (сведения)

Шаблонный код для страниц учащихся не включает в себя сведения о регистрации. В этом разделе регистрации добавляются на страницу сведений.

### <a name="read-enrollments"></a>Считывание сведений о регистрации

Чтобы отобразить сведения о регистрации учащегося на странице, необходимо считать их. Шаблонный код в файле *Pages/Students/Details.cshtml.cs* считывает только сведения об учащихся, но не сведения о регистрации:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Замените метод `OnGetAsync` приведенным ниже кодом для считывания сведений о регистрации для выбранного учащегося. Изменения выделены.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Методы [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) и [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) инструктируют контекст для загрузки свойства навигации `Student.Enrollments`, а также свойства навигации `Enrollment.Course` в пределах каждой регистрации. Эти методы более подробно рассматриваются в учебнике, посвященном [чтению связанных данных](xref:data/ef-rp/read-related-data).

Метод [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) повышает производительность в тех сценариях, где возвращаемые сущности не обновляются в текущем контексте. `AsNoTracking` рассматривается позднее в этом учебнике.

### <a name="display-enrollments"></a>Отображение регистраций

Замените код в файле *Pages/Students/Details.cshtml* приведенным ниже кодом для отображения списка регистраций. Изменения выделены.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Приведенный выше код циклически обрабатывает сущности в свойстве навигации `Enrollments`. Для каждой регистрации он отображает название курса и оценку. Название курса извлекается из сущности Course, которая хранится в свойстве навигации `Course` сущности Enrollments.

Запустите приложение, выберите вкладку **Students** (Учащиеся) и щелкните ссылку **Details** (Сведения) для учащегося. Отобразится список курсов и оценок для выбранного учащегося.

### <a name="ways-to-read-one-entity"></a>Способы чтения одной сущности

В созданном коде для чтения одной сущности используется метод [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_). Этот метод возвращает значение NULL, если ничего не найдено. В противном случае возвращается первая найденная строка, удовлетворяющая условиям фильтра запросов. `FirstOrDefaultAsync` обычно предпочтительнее, чем следующие варианты:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) — вызывает исключение при наличии нескольких сущностей, соответствующих фильтру запросов. Для определения того, может ли запрос вернуть более одной строки, `SingleOrDefaultAsync` пытается получить несколько строк. Это дополнительное действие не требуется, если запрос может вернуть только одну сущность, как при поиске по уникальному ключу.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) — находит сущность с первичным ключом. Если сущность с первичным ключом отслеживается контекстом, она возвращается без запроса к базе данных. Этот метод оптимизирован для поиска одной сущности, однако вызвать `Include` с `FindAsync` невозможно.  Поэтому если требуются связанные данные, лучше использовать `FirstOrDefaultAsync`.

### <a name="route-data-vs-query-string"></a>Данные маршрута или строка запроса

URL-адрес страницы сведений — `https://localhost:<port>/Students/Details?id=1`. Значение первичного ключа сущности содержится в строке запроса. Некоторые разработчики предпочитают передавать значение ключа в данных маршрута: `https://localhost:<port>/Students/Details/1`. Дополнительные сведения см. в разделе [Обновление созданного кода](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Обновление страницы Create

Шаблонный код `OnPostAsync` для страницы создания уязвим к [чрезмерной передаче данных](#overposting). Замените метод `OnPostAsync` в файле *Pages/Students/Create.cshtml.cs*, используя следующий код:

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

В приведенном выше коде создается объект Student, после чего опубликованные поля формы используются для обновления свойств этого объекта. Метод [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) выполняет указанные ниже действия.

* Использует опубликованные значения формы из свойства [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) в [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Обновляет только перечисленные свойства (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* Ищет поля формы с префиксом "student". Например, `Student.FirstMidName`. Задается без учета регистра символов.
* Использует систему [привязки модели](xref:mvc/models/model-binding) для преобразования значений формы из строк в типы модели `Student`. Например, `EnrollmentDate` следует преобразовать в тип DateTime.

Запустите приложение и создайте сущность учащегося для тестирования страницы создания.

## <a name="overposting"></a>Чрезмерная передача данных

В целях повышения безопасности рекомендуется использовать `TryUpdateModel` для обновления полей на основе отправленных значений, поскольку в этом случае исключается чрезмерная передача данных. Например, сущность Student включает свойство `Secret`, которое веб-страница не должна обновлять или добавлять:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Даже если приложение не имеет поля `Secret` на странице создания или обновления Razor, злоумышленник может установить значение `Secret` посредством чрезмерной передачи данных. Злоумышленник может использовать такие средства, как Fiddler, или собственный код JavaScript для отправки значения формы `Secret`. В исходном коде не ограничиваются поля, которые используются при создании экземпляра Student связывателем модели.

Какое бы значение ни задал злоумышленник для поля формы `Secret`, оно будет обновлено в базе данных. На следующем рисунке показано средство Fiddler, с помощью которого в отправленные значения формы добавляется поле `Secret` (со значением "OverPost").

![Добавление поля Secret с помощью средства Fiddler](../ef-mvc/crud/_static/fiddler.png)

Значение "OverPost" успешно добавлено в свойство `Secret` вставленной строки. Это происходит несмотря на то, что разработчик приложения не планировал, что свойство `Secret` будет устанавливаться на странице создания.

### <a name="view-model"></a>Модель представления

Модели представления реализуют альтернативный подход к защите от чрезмерной передачи данных.

Модель приложения часто называют моделью домена. Модель предметной области обычно содержит все свойства, необходимые для соответствующей сущности в базе данных. Модель представления содержит только те свойства, которые необходимы соответствующему пользовательскому интерфейсу (например, странице создания).

Помимо модели представления в некоторых приложениях используется модель привязки или модель ввода для передачи данных между классом модели страницы Razor Pages и браузером. 

Рассмотрим следующую модель представления `Student`:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

В следующем коде модель представления используется `StudentVM` для создания нового учащегося:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Метод [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) устанавливает значения этого объекта, считывая значения из другого объекта [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` использует сопоставление имен свойств. Тип модели представления может быть не связан с типом модели, однако они должны содержать совпадающие свойства.

При использовании `StudentVM` необходимо обновить [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml), чтобы использовать `StudentVM` вместо `Student`.

## <a name="update-the-edit-page"></a>Обновление страницы редактирования

В файле *Pages/Students/Edit.cshtml.cs* замените методы `OnGetAsync` и `OnPostAsync` приведенным ниже кодом.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Изменения в коде аналогичны странице Create за некоторыми исключениями:

* `FirstOrDefaultAsync` заменен на [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Если включать связанные данные не требуется, более эффективным будет метод `FindAsync`.
* `OnPostAsync` имеет параметр `id`.
* Текущий учащийся извлекается из базы данных вместо того, чтобы создавать нового учащегося.

Запустите приложение и протестируйте его, создав и изменив учащегося.

## <a name="entity-states"></a>Состояния сущностей

Контекст базы данных отслеживает синхронизацию сущностей в памяти с соответствующими им строками в базе данных. Данные отслеживания определяют поведение при вызове метода [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_). Например, при передаче новой сущности в метод [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) ей присваивается состояние [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). При вызове метода `SaveChangesAsync` контекст базы данных выполняет команду SQL INSERT.

Возможны следующие [состояния сущности](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`. Сущность еще не существует в базе данных. Метод `SaveChanges` выполняет инструкцию INSERT.

* `Unchanged`. изменения сущности не сохраняются. Сущность находится в этом состоянии при считывании из базы данных.

* `Modified`. Были изменены значения некоторых или всех свойств сущности. Метод `SaveChanges` выполняет инструкцию UPDATE.

* `Deleted`. Сущность отмечена для удаления. Метод `SaveChanges` выполняет инструкцию DELETE.

* `Detached`. Сущность не отслеживается контекстом базы данных.

В классическом приложении изменения состояния обычно осуществляются автоматически. После считывания сущности и ее изменения ей автоматически присваивается состояние `Modified`. При вызове метода `SaveChanges` создается инструкция SQL UPDATE, которая обновляет только измененные свойства.

В веб-приложении объект `DbContext`, который считывает сущность и отображает ее данные, ликвидируется после отрисовки страницы. При вызове метода страницы `OnPostAsync` выполняется новый веб-запрос с новым экземпляром `DbContext`. Если повторно считать сущность в этот новый контекст, будет смоделирована обработка в классическом приложении.

## <a name="update-the-delete-page"></a>Обновление страницы удаления

В этом разделе реализуется пользовательское сообщение об ошибке на случай сбоя при вызове `SaveChanges`.

Замените код в файле *Pages/Students/Delete.cshtml.cs* на приведенный ниже. Изменения выделены (кроме очистки инструкций `using`).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

В предыдущем коде в сигнатуру метода `OnGetAsync` добавляется необязательный параметр `saveChangesError`. `saveChangesError` указывает, был ли метод вызван после того, как произошел сбой при удалении объекта учащегося. Операция удаления может завершиться сбоем из-за временных проблем с сетью. Вероятность возникновения временных проблем с сетью выше, когда база данных размещается в облаке. Параметр `saveChangesError` имеет значение false при вызове метода `OnGetAsync` страницы удаления из пользовательского интерфейса. Если `OnGetAsync` вызывается методом `OnPostAsync` (из-за сбоя операции удаления), параметру `saveChangesError` присваивается значение true.

Метод `OnPostAsync` извлекает выбранную сущность и вызывает метод [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_), чтобы присвоить ей состояние `Deleted`. При вызове метода `SaveChanges` создается инструкция SQL DELETE. В случае сбоя `Remove`:

* Возникает исключение базы данных.
* Вызывается метод `OnGetAsync` страницы Delete с параметром `saveChangesError=true`.

Добавьте сообщение об ошибке на страницу удаления Razor (*Pages/Students/Delete.cshtml*).

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Запустите приложение и удалите учащегося, чтобы протестировать страницу удаления.

## <a name="next-steps"></a>Следующие шаги

> [!div class="step-by-step"]
> [Предыдущий учебник](xref:data/ef-rp/intro)
> [Следующий учебник](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом учебнике описывается проверка и настройка шаблонного кода операций CRUD (создание, чтение, обновление и удаление).

Чтобы максимально упростить этот код и сконцентрироваться на работе с EF Core, в моделях страниц в этих учебниках используется код EF Core. Некоторые разработчики используют уровень служб или шаблон репозитория, чтобы создать уровень абстракции между пользовательским интерфейсом (Razor Pages) и уровнем доступа к данным.

В рамках этого руководства изучаются страницы RazorPages в папке *Students*, предназначенные для создания, редактирования, удаления и просмотра сведений.

В шаблонном коде используется следующий шаблон для страниц создания, редактирования и удаления:

* Получение и отображение запрашиваемых данных с помощью метода HTTP GET`OnGetAsync`.
* Сохранение изменений в данных с помощью метода HTTP POST`OnPostAsync`.

Страницы указателя и сведений получают и отображают запрашиваемые данные с помощью метода HTTP GET`OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync и FirstOrDefaultAsync

В созданном коде используется [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), что, как правило, предпочтительнее использования [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 При выборке одной сущности `FirstOrDefaultAsync` демонстрирует более высокую эффективность `SingleOrDefaultAsync`:

* Если в коде не требуется проверять, что запрос возвращает не более одной сущности.
* `SingleOrDefaultAsync` выбирает больше данных и выполняет ненужные операции.
* `SingleOrDefaultAsync` вызывает исключение при наличии нескольких сущностей, соответствующих части фильтра.
* `FirstOrDefaultAsync` на вызывает исключение при наличии нескольких сущностей, соответствующих части фильтра.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

Чаще всего в шаблонном коде [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) можно использовать вместо `FirstOrDefaultAsync`.

`FindAsync`:

* Находит сущность с первичным ключом. Если сущность с первичным ключом отслеживается контекстом, она возвращается без запроса к базе данных.
* Является простым и быстрым.
* Оптимизирован для поиска одной сущности.
* В некоторых ситуациях может давать преимущества в производительности, однако редко используется в обычных веб-приложениях.
* Неявно использует [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) вместо [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Однако если необходимо включить (`Include`) другие сущности, `FindAsync` более не будет подходить. Это значит, что по мере работы приложения может потребоваться отменить `FindAsync` и перейти к запросу.

## <a name="customize-the-details-page"></a>Настройка страницы сведений

Перейдите на страницу `Pages/Students`. Ссылки **Edit**, **Details** и **Delete** создаются [вспомогательной функцией тегов привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) в файле *Pages/Students/Index.cshtml*.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Запустите приложение и щелкните ссылку **Details**. URL-адрес имеет вид `http://localhost:5000/Students/Details?id=2`. Идентификатор учащегося передается с помощью строки запроса (`?id=2`).

Обновите страницы Razor Pages Edit, Details и Delete так, чтобы использовался шаблон маршрута `"{id:int}"`. Измените директиву страницы для каждой из этих страниц c `@page` на `@page "{id:int}"`.

Запрос к странице с шаблоном маршрута "{id:int}", который **не** включает в себя целочисленное значение маршрута, приводит к ошибке HTTP 404 (не найдено). Например, `http://localhost:5000/Students/Details` возвращает ошибку 404. Чтобы сделать идентификатор необязательным, добавьте `?` к ограничению маршрута:

 ```cshtml
@page "{id:int?}"
```

Запустите приложение, щелкните ссылку Details и убедитесь, что в URL-адресе в виде данных маршрута передается идентификатор (`http://localhost:5000/Students/Details/2`).

Не выполняйте глобальную замену `@page` на `@page "{id:int}"`, поскольку это приведет к нарушению ссылок на страницы Home и Create.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Добавление связанных данных

Шаблонный код страницы указателя учащихся не включает свойство `Enrollments`. В этом разделе на странице Details отображается содержимое коллекции `Enrollments`.

Метод `OnGetAsync` в файле *Pages/Students/Details.cshtml.cs* использует метод `FirstOrDefaultAsync` для извлечения одной сущности `Student`. Добавьте выделенный ниже код:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Методы [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) и [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) инструктируют контекст для загрузки свойства навигации `Student.Enrollments`, а также свойства навигации `Enrollment.Course` в пределах каждой регистрации. Эти методы более подробно рассматриваются в учебнике, посвященном чтению данных.

Метод [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) повышает производительность в тех сценариях, где возвращаемые сущности не обновляются в текущем контексте. `AsNoTracking` рассматривается позднее в этом учебнике.

### <a name="display-related-enrollments-on-the-details-page"></a>Отображение связанных регистраций на странице Details

Откройте файл *Pages/Students/Details.cshtml*. Добавьте выделенный ниже код, чтобы отобразить список регистраций:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Если после вставки кода нарушаются отступы в нем, нажмите клавиши CTRL-K-D, чтобы исправить это.

Приведенный выше код циклически обрабатывает сущности в свойстве навигации `Enrollments`. Для каждой регистрации он отображает название курса и оценку. Название курса извлекается из сущности Course, которая хранится в свойстве навигации `Course` сущности Enrollments.

Запустите приложение, выберите вкладку **Students** (Учащиеся) и щелкните ссылку **Details** (Сведения) для учащегося. Отобразится список курсов и оценок для выбранного учащегося.

## <a name="update-the-create-page"></a>Обновление страницы Create

Измените метод `OnPostAsync` в файле *Pages/Students/Create.cshtml.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Проверьте код [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_):

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

В приведенном выше коде `TryUpdateModelAsync<Student>` пытается обновить объект `emptyStudent`, используя отправленные значения формы из свойства [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) в [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync` обновляет только перечисленные свойства (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

В предыдущем примере:

* Второй аргумент (`"student", // Prefix`) представляет собой префикс для поиска значений. Задается без учета регистра символов.
* Отправленные значения формы преобразуются в типы в модели `Student` с использованием [привязки модели](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Чрезмерная передача данных

В целях повышения безопасности рекомендуется использовать `TryUpdateModel` для обновления полей на основе отправленных значений, поскольку в этом случае исключается чрезмерная передача данных. Например, сущность Student включает свойство `Secret`, которое веб-страница не должна обновлять или добавлять:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Даже если приложение не имеет поля `Secret` на странице создания или обновления Razor, злоумышленник может установить значение `Secret` посредством чрезмерной передачи данных. Злоумышленник может использовать такие средства, как Fiddler, или собственный код JavaScript для отправки значения формы `Secret`. В исходном коде не ограничиваются поля, которые используются при создании экземпляра Student связывателем модели.

Какое бы значение ни задал злоумышленник для поля формы `Secret`, оно будет обновлено в базе данных. На следующем рисунке показано средство Fiddler, с помощью которого в отправленные значения формы добавляется поле `Secret` (со значением "OverPost").

![Добавление поля Secret с помощью средства Fiddler](../ef-mvc/crud/_static/fiddler.png)

Значение "OverPost" успешно добавлено в свойство `Secret` вставленной строки. Разработчик приложения не планировал, что свойство `Secret` будет устанавливаться на странице Create.

<a name="vm"></a>

### <a name="view-model"></a>Модель представления

Модель представления обычно содержит подмножество свойств, которые включены в модель и используются приложением. Модель приложения часто называют моделью домена. Модель домена обычно содержит все свойства, необходимые для соответствующей сущности в базе данных. Модель представления содержит только те свойства, которые необходимы уровню пользовательского интерфейса (например, на странице Create). Помимо модели представления в некоторых приложениях используется модель привязки или модель ввода для передачи данных между классом модели страницы Razor Pages и браузером. Рассмотрим следующую модель представления `Student`:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Модели представления реализуют альтернативный подход к защите от чрезмерной передачи данных. Модель представления содержит только свойства для просмотра (отображения) или обновления.

В следующем коде модель представления используется `StudentVM` для создания нового учащегося:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Метод [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) устанавливает значения этого объекта, считывая значения из другого объекта [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` использует сопоставление имен свойств. Тип модели представления может быть не связан с типом модели, однако они должны содержать совпадающие свойства.

При использовании `StudentVM` необходимо обновить [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml), чтобы использовать `StudentVM` вместо `Student`.

В Razor Pages представление модели реализуется с помощью производного класса `PageModel`.

## <a name="update-the-edit-page"></a>Обновление страницы редактирования

Обновите модель страницы Edit. Основные изменения выделены:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Изменения в коде аналогичны странице Create за некоторыми исключениями:

* `OnPostAsync` имеет необязательный параметр `id`.
* Текущий учащийся извлекается из базы данных вместо того, чтобы создавать нового учащегося.
* `FirstOrDefaultAsync` заменен на [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync` лучше использовать при выборе сущности из первичного ключа. Дополнительные сведения см. в разделе [FindAsync](#FindAsync).

### <a name="test-the-edit-and-create-pages"></a>Проверка страниц редактирования и создания

Создайте и измените несколько сущностей учащихся.

## <a name="entity-states"></a>Состояния сущностей

Контекст базы данных отслеживает синхронизацию сущностей в памяти с соответствующими им строками в базе данных. Сведения о синхронизации контекста базы данных определяют поведение при вызове метода [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_). Например, при передаче новой сущности в метод [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) ей присваивается состояние [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). При вызове метода `SaveChangesAsync` контекст базы данных выполняет команду SQL INSERT.

Возможны следующие [состояния сущности](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`. сущность еще не существует в базе данных. Метод `SaveChanges` выполняет инструкцию INSERT.

* `Unchanged`. изменения сущности не сохраняются. Сущность находится в этом состоянии при считывании из базы данных.

* `Modified`. Были изменены значения некоторых или всех свойств сущности. Метод `SaveChanges` выполняет инструкцию UPDATE.

* `Deleted`. Сущность отмечена для удаления. Метод `SaveChanges` выполняет инструкцию DELETE.

* `Detached`. сущность не отслеживается контекстом базы данных.

В классическом приложении изменения состояния обычно осуществляются автоматически. После считывания сущности и ее изменения ей автоматически присваивается состояние `Modified`. При вызове метода `SaveChanges` создается инструкция SQL UPDATE, которая обновляет только измененные свойства.

В веб-приложении объект `DbContext`, который считывает сущность и отображает ее данные, ликвидируется после отрисовки страницы. При вызове метода страницы `OnPostAsync` выполняется новый веб-запрос с новым экземпляром `DbContext`. Если повторно считать сущность в этот новый контекст, таким образом будет смоделирована обработка в классическом приложении.

## <a name="update-the-delete-page"></a>Обновление страницы удаления

В этом разделе добавляется код, реализующий настраиваемое сообщение ошибки на случай сбоя при вызове `SaveChanges`. Добавьте строку, содержащую возможные сообщения об ошибке:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Замените метод `OnGetAsync` следующим кодом:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Приведенный выше код содержит необязательный параметр `saveChangesError`. `saveChangesError` указывает, был ли метод вызван после того, как произошел сбой при удалении объекта учащегося. Операция удаления может завершиться сбоем из-за временных проблем с сетью. Вероятность возникновения временных проблем с сетью выше в облаке. `saveChangesError` имеет значение false при вызове `OnGetAsync` страницы Delete из пользовательского интерфейса. Если `OnGetAsync` вызывается методом `OnPostAsync` (из-за сбоя операции удаления), параметру `saveChangesError` присваивается значение true.

### <a name="the-delete-pages-onpostasync-method"></a>Метод OnPostAsync страницы Delete

Замените `OnPostAsync` следующим кодом:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Приведенный выше код извлекает выбранную сущность и вызывает метод [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_), чтобы присвоить ей состояние `Deleted`. При вызове метода `SaveChanges` создается инструкция SQL DELETE. В случае сбоя `Remove`:

* Вызывается исключение базы данных.
* Вызывается метод `OnGetAsync` страницы Delete с параметром `saveChangesError=true`.

### <a name="update-the-delete-no-locrazor-page"></a>Обновление страницы удаления Razor

Добавьте выделенное ниже сообщение об ошибке на страницу Delete Razor.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Проверьте удаление.

## <a name="common-errors"></a>Распространенные ошибки

Не работает ссылка Students/Index или другие ссылки:

Убедитесь, что на странице Razor содержится правильная директива `@page`. Например, страница Razor Students/Index **не** должна содержать шаблон маршрута:.

```cshtml
@page "{id:int}"
```

Каждая страница Razor должна содержать директиву `@page`.



## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Назад](xref:data/ef-rp/intro)
> [Вперед](xref:data/ef-rp/sort-filter-page)

::: moniker-end
