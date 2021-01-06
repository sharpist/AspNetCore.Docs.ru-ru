---
title: Часть 5. Razor Pages с EF Core в ASP.NET Core — модель данных
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054103"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a>Часть 5. Razor Pages с EF Core в ASP.NET Core — модель данных

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Предыдущие руководства работали с базовой моделью данных, состоящей из трех сущностей. В этом руководстве:

* Добавляются дополнительные сущности и связи.
* Настраивается модель данных с помощью указания правил для форматирования, проверки и сопоставления базы данных.

Готовая модель данных показана на следующем рисунке:

![Схема сущностей](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Сущность Student

![Сущность Student](complex-data-model/_static/student-entity.png)

Замените код в файле *Models/Student.cs* на следующий:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

В приведенном выше коде добавляется свойство `FullName` и добавляются следующие атрибуты к существующим свойствам:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>Вычисляемое свойство FullName

`FullName` — это вычисляемое свойство, которое возвращает значение, созданное путем объединения двух других свойств. `FullName` не может быть задано, поэтому имеет только метод доступа get. В базе данных не создается столбец `FullName`.

### <a name="the-datatype-attribute"></a>Атрибут DataType

```csharp
[DataType(DataType.Date)]
```

Сейчас для дат зачисления учащихся на всех страницах отображаются время и дата, хотя важна только дата. Используя атрибуты заметок к данным, вы можете внести в код одно изменение, позволяющее исправить формат отображения на каждой странице, где отображаются эти данные. 

Атрибут [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) указывает тип данных более точно по сравнению со встроенным типом базы данных. В этом случае следует отображать отобразить только дату, а не дату и время. В [перечислении DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) представлено множество типов данных, таких как Date, Time, PhoneNumber, Currency, EmailAddress и других. Атрибут `DataType` также обеспечивает автоматическое предоставление функций для определенных типов в приложении. Пример:

* Ссылка `mailto:` для `DataType.EmailAddress` создается автоматически.
* Средство выбора даты предоставляется для `DataType.Date` в большинстве браузеров.

Атрибут `DataType` создает атрибуты HTML 5 `data-`. Атрибуты `DataType` не предназначены для проверки.

### <a name="the-displayformat-attribute"></a>Атрибут DisplayFormat

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` не задает формат отображаемой даты. По умолчанию поле даты отображается с использованием форматов, установленных в [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) сервера.

С помощью атрибута `DisplayFormat` можно явно указать формат даты: Параметр `ApplyFormatInEditMode` указывает, что формат должен применяться к пользовательскому интерфейсу редактирования. Некоторым полям не следует использовать `ApplyFormatInEditMode`. Например, обозначение денежной единицы в общем случае не должно отображаться в поле редактирования текста.

Атрибут `DisplayFormat` можно использовать отдельно. Однако чаще всего `DataType` рекомендуется применять вместе с атрибутом `DisplayFormat`. Атрибут `DataType` передает семантику данных (в отличие от способа их вывода на экран). Атрибут `DataType` дает следующие преимущества, недоступные в `DisplayFormat`:

* Поддержка функций HTML5 в браузере. Например, отображение элемента управления календарем, соответствующего языковому стандарту обозначения денежной единицы, ссылок электронной почты, проверки входных данных на стороне клиента.
* По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.

Дополнительные сведения см. в документации по [вспомогательной функции тегов \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>Атрибут StringLength

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

С помощью атрибутов можно указать правила проверки данных и сообщения об ошибках проверки. Атрибут [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) указывает минимальную и максимальную длину символов, разрешенных в поле данных. Представленный код задает ограничение длины имен в 50 символов. Пример, в котором задается минимальная длина строки, приводится [далее](#the-required-attribute).

Атрибут `StringLength` также обеспечивает проверку на стороне клиента и на стороне сервера. Минимальное значение не оказывает влияния на схему базы данных.

Атрибут `StringLength` не запрещает пользователю ввести пробел в качестве имени пользователя. Атрибут [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) можно использовать для применения ограничений к входным данным. Например, следующий код требует, чтобы первый символ был прописной буквой, а остальные символы были буквенными:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **обозревателе объектов SQL Server** (SSOX) откройте конструктор таблиц учащихся, дважды щелкнув таблицу **Student** (Учащийся).

![Таблица учащихся в SSOX до миграций](complex-data-model/_static/ssox-before-migration.png)

На предыдущем изображении показана схемы для таблицы `Student`. Поля имен имеют тип `nvarchar(MAX)`. Когда далее в этом учебнике будет создана и применена миграция, поля имен станут `nvarchar(50)` из-за атрибутов длины строки.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В средстве SQLite изучите определения столбцов для таблицы `Student`. Поля имен имеют тип `Text`. Обратите внимание, что первое поле имени называется `FirstMidName`. В следующем разделе вы измените имя этого столбца на `FirstName`.

---

### <a name="the-column-attribute"></a>Атрибут Column

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Атрибуты могут управлять, как именно классы и свойства сопоставляются с базой данных. В модели `Student` атрибут `Column` используется для сопоставления имени свойства `FirstMidName` с "FirstName" в базе данных.

При создании базы данных имена свойств в модели используются для имен столбцов (кроме случая, когда используется атрибут `Column`). Модель `Student` использует `FirstMidName` для поля имени, так как это поле также может содержать отчество.

С атрибутом `[Column]` поле `Student.FirstMidName` в модели данных сопоставляется со столбцом `FirstName` таблицы `Student`. Добавление атрибута `Column` изменяет модель для поддержки `SchoolContext`. Модель, поддерживающая `SchoolContext`, больше не соответствует базе данных. Это несоответствие будет устранено путем добавления миграции далее в этом учебнике.

### <a name="the-required-attribute"></a>Атрибут Required

```csharp
[Required]
```

Атрибут `Required` делает свойства имен обязательными полями. Атрибут `Required` не нужен для типов, не допускающих значения NULL, например для типов значений (таких как `DateTime`, `int` и `double`). Типы, которые не могут принимать значение null, автоматически обрабатываются как обязательные поля.

Для применения `MinimumLength` нужно использовать атрибут `Required` с `MinimumLength`.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` и `Required` разрешают использовать пробелы при проверке. Используйте атрибут `RegularExpression` для полного контроля над строкой.

### <a name="the-display-attribute"></a>Атрибут Display

```csharp
[Display(Name = "Last Name")]
```

Атрибут `Display` указывает, что заголовки для текстовых полей должны иметь вид "First Name" (Имя), "Last Name" (Фамилия), "Full Name" (Полное имя) и "Enrollment Date" (Дата зачисления). По умолчанию в заголовках не использовался пробел для разделения слов, например "Lastname".

### <a name="create-a-migration"></a>Создание миграции

Запустите приложение и перейдите на страницу Students. Возникает исключение. Атрибут `[Column]` приводит к тому, что EF ожидает столбец с именем `FirstName`, но имя столбца в базе данных по-прежнему `FirstMidName`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Сообщение об ошибке подобно приведенному ниже.

```
SqlException: Invalid column name 'FirstName'.
```

* В PMC введите следующие команды для создания миграции и обновления базы данных:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Первая из этих команд выдает следующее предупреждающее сообщение:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Это предупреждение вызвано тем, что поля имен теперь ограничены 50 символами. Если имя в базе данных имеет больше 50 символов, символы с 51-го до последнего будут потеряны.

* Откройте таблицу "Student" (Учащийся) в окне SSOX:

  ![Таблица учащихся в SSOX после миграций](complex-data-model/_static/ssox-after-migration.png)

  До применения миграции столбцы имен имели тип [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Теперь столбцы имен имеют тип `nvarchar(50)`. Имя столбца изменилось с `FirstMidName` на `FirstName`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Сообщение об ошибке подобно приведенному ниже.

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Откройте командное окно в папке проекта. Введите следующие команды для создания миграции и обновления базы данных:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Команда обновления базы данных выводит ошибку наподобие приведенной ниже.

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

В этом учебнике устранить ошибку можно, удалив и заново создав исходную миграцию. Дополнительные сведения см. в предупреждении, касающемся SQLite, в начале [учебника по миграции](xref:data/ef-rp/migrations).

* Удалите папку *Migrations*.
* Выполните следующие команды, чтобы удалить базу данных, создать новую исходную миграцию и применить ее:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* Изучите таблицу Student в средстве SQLite. Столбец, ранее называвшийся FirstMidName, теперь имеет имя FirstName.

---

* Запустите приложение и перейдите на страницу Students.
* Обратите внимание, что значения времени не вводятся и не отображаются вместе с датами.
* Выберите **Create New** (Создать) и попробуйте ввести имя длиной более 50 символов.

> [!Note]
> В следующих разделах сборка приложения на некоторых этапах приводит к возникновению ошибок компилятора. В инструкциях указано, когда производить сборку приложения.

## <a name="the-instructor-entity"></a>Сущность Instructor

![Сущность Instructor](complex-data-model/_static/instructor-entity.png)

Создайте файл *Models/Instructor.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

На одной строке могут находиться несколько атрибутов. Атрибуты `HireDate` можно записать следующим образом:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Свойства навигации

`CourseAssignments` и `OfficeAssignment` — это свойства навигации.

Преподаватель может проводить любое количество курсов, поэтому `CourseAssignments` определен как коллекция.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Преподаватель может иметь не более одного кабинета, поэтому свойство `OfficeAssignment` содержит одну сущность `OfficeAssignment`. `OfficeAssignment` имеет значение null, если кабинет не назначен.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>Сущность OfficeAssignment

![Сущность OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Создайте файл *Models/OfficeAssignment.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Атрибут Key

Атрибут `[Key]` используется для идентификации свойства в качестве первичного ключа (PK), когда имя свойства отличается от classnameID или ID.

Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному. Назначение кабинета существует только в связи с преподавателем, которому оно назначено. Первичный ключ `OfficeAssignment` также является внешним ключом (FK) для сущности `Instructor`.

EF Core не распознает `InstructorID` в качестве первичного ключа `OfficeAssignment` автоматически, так как `InstructorID` не соответствует соглашению об именовании ID или classnameID. Таким образом, атрибут `Key` используется для определения `InstructorID` в качестве первичного ключа:

```csharp
[Key]
public int InstructorID { get; set; }
```

По умолчанию EF Core считает ключ созданным не базой данных, так как столбец предназначен для идентифицирующего отношения.

### <a name="the-instructor-navigation-property"></a>Свойство навигации Instructor

Свойство навигации `Instructor.OfficeAssignment` может иметь значение NULL, так как строка `OfficeAssignment` для преподавателя может отсутствовать. Преподавателю может быть не назначен кабинет.

Свойство навигации `OfficeAssignment.Instructor` всегда будет иметь сущность Instructor, так как тип внешнего ключа `InstructorID` — это тип значения `int`, не допускающий значения NULL. Назначение кабинета не может существовать без преподавателя.

Когда сущность `Instructor` имеет связанную сущность `OfficeAssignment`, каждая из них имеет ссылку на другую в своем свойстве навигации.

## <a name="the-course-entity"></a>Сущность Course

![Сущность Course](complex-data-model/_static/course-entity.png)

Обновите *Models/Course.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

Сущность `Course` имеет свойство внешнего ключа (FK) `DepartmentID`. `DepartmentID` указывает на связанную сущность `Department`. Сущность `Course` имеет свойство навигации `Department`.

EF Core не требует свойства внешнего ключа для модели данных, если она имеет свойство навигации для связанной сущности. EF Core автоматически создает внешние ключи в базе данных по мере необходимости. EF Core создает [теневые свойства](/ef/core/modeling/shadow-properties) для автоматически созданных внешних ключей. Однако явное включение внешнего ключа в модель данных позволяет сделать обновления проще и эффективнее. Например, рассмотрим модель, где свойство внешнего ключа `DepartmentID`*не* включено. При получении сущности курса для редактирования:

* свойство `Department` имеет значение NULL, если оно не загружено явно;
* для обновления сущности курса нужно сначала получить сущность `Department`.

Если свойство внешнего ключа `DepartmentID` включено в модель данных, получать сущность `Department` перед обновлением не нужно.

### <a name="the-databasegenerated-attribute"></a>Атрибут DatabaseGenerated

Атрибут `[DatabaseGenerated(DatabaseGeneratedOption.None)]` указывает, что первичный ключ предоставляется приложением, а не создается базой данных.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

По умолчанию EF Core предполагает, что значения первичного ключа создаются базой данных. Обычно лучше всего использовать значения, созданные базой данных. Для сущностей `Course` пользователь указывает первичный ключ. Например, номер курса, такой как серия 1000 для кафедры математики и серия 2000 для кафедры английского языка.

Атрибут `DatabaseGenerated` также может использоваться для создания значений по умолчанию. Например, база данных может автоматически создать поле даты для записи даты, когда строка была создана или изменена. Дополнительные сведения см. в разделе [Созданные свойства](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа (FK) и свойства навигации в сущности `Course` отражают следующие связи:

Курс назначается одной кафедре, поэтому имеется внешний ключ `DepartmentID` и свойство навигации `Department`.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

На курс может быть зачислено любое количество учащихся, поэтому свойство навигации `Enrollments` является коллекцией:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Курс могут вести несколько преподавателей, поэтому свойство навигации `CourseAssignments` является коллекцией:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` описано [далее](#many-to-many-relationships).

## <a name="the-department-entity"></a>Сущность Department

![Сущность Department](complex-data-model/_static/department-entity.png)

Создайте файл *Models/Department.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Атрибут Column

Ранее атрибут `Column` использовался, чтобы изменить сопоставление имени столбца. В коде для сущности `Department` атрибут `Column` можно использовать, чтобы изменить сопоставление типов данных SQL. Столбец `Budget` определяется с помощью типа money SQL Server в базе данных:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Сопоставление столбцов обычно не требуется. EF Core выбирает соответствующий тип данных SQL Server на основе типа среды CLR для свойства. Тип `decimal` среды CLR сопоставляется с типом `decimal` SQL Server. `Budget` используется для денежных единиц, хотя для этого лучше подходит тип данных money.

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа и навигации отражают следующие связи:

* Кафедра может иметь или не иметь администратора.
* Администратор всегда является преподавателем. Поэтому свойство `InstructorID` включается в качестве внешнего ключа в сущность `Instructor`.

Свойство навигации называется `Administrator`, но содержит сущность `Instructor`:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Вопросительный знак (?) в приведенном выше коде указывает, что свойство допускает значение null.

Кафедра может иметь несколько курсов, поэтому доступно свойство навигации Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

По соглашению EF Core разрешает каскадное удаление для внешних ключей, не допускающих значение null, и связей "многие ко многим". Это поведение по умолчанию может привести к циклическим правилам каскадного удаления. Такие правила вызывают исключение при добавлении миграции.

Например, если свойство `Department.InstructorID` было определено как не допускающее значения NULL, EF Core настроит правило каскадного удаления. В этом случае кафедра будет удалена, если будет удален преподаватель, назначенный ее администратором. В такой ситуации правило ограничения будет более целесообразным. Приведенный ниже [текучий API](#fluent-api-alternative-to-attributes) задает правило ограничения и отключает правило каскадного удаления.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Сущность Enrollment

Запись зачисления обозначает один курс, который проходит один учащийся.

![Сущность Enrollment](complex-data-model/_static/enrollment-entity.png)

Обновите *Models/Enrollment.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа и навигации отражают следующие связи:

Запись зачисления предназначена для одного курса, поэтому доступно свойство первичного ключа `CourseID` и свойство навигации `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Запись зачисления предназначена для одного учащегося, поэтому доступно свойство первичного ключа `StudentID` и свойство навигации `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Связи многие ко многим

Между сущностями `Student` и `Course` действует связь многие ко многим. Сущности `Enrollment` выступают в качестве таблицы соединения многие ко многим *с полезными данными* в базе данных. Фраза "с полезными данными" означает, что таблица `Enrollment` содержит дополнительные данные, кроме внешних ключей для присоединяемых таблиц (в данном случае — первичный ключ и `Grade`).

На следующем рисунке показано, как выглядят эти связи на схеме сущностей. (Эта схема была создана с помощью [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) для EF 6.x. Создание схемы не является частью руководства.)

![Связь многие ко многим между Student и Course](complex-data-model/_static/student-course.png)

Каждая линия связи имеет 1 на одном конце и звездочку (*) на другом, указывая характер один ко многим.

Если таблица `Enrollment` не включала в себя сведения об оценках, ей потребуется содержать всего два внешних ключа (`CourseID` и `StudentID`). Таблицу соединения многие ко многим без полезных данных иногда называют чистой таблицей соединения (PJT).

Сущности `Instructor` и `Course` имеют связь многие ко многим с использованием чистой таблицы соединения.

Примечание. EF 6.x поддерживает неявные таблицы соединения для связей многие ко многим, но EF Core — нет. Дополнительные сведения см. в статье [Связи многие ко многим в EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Сущность CourseAssignment

![Сущность CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Создайте файл *Models/CourseAssignment.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Для связи "многие ко многим" между Instructor и Courses нужна таблица соединения, сущностью для которой является CourseAssignment.

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

Обычно для сущности соединения используется имя `EntityName1EntityName2`. Например, таблицей соединения Instructor и Courses, использующей этот шаблон, будет `CourseInstructor`. Однако рекомендуется использовать имя, которое описывает эту связь.

Модели данных создаются простыми и разрастаются. Таблицы соединения без полезных данных (PJT) часто начинают включать их. Если в начале задать описательное имя сущности, его не нужно менять при изменениях таблицы соединения. Оптимально, если сущность соединения имеет собственное естественное имя (возможно, из одного слова) в бизнес-среде. Например, Books и Customers можно связать с сущностью соединения Ratings. Связь многие ко многим между Instructor и Courses `CourseAssignment` является более предпочтительным вариантом, чем `CourseInstructor`.

### <a name="composite-key"></a>Составной ключ

Два внешних ключа в `CourseAssignment` (`InstructorID` и `CourseID`) совместно однозначно определяют каждую строку таблицы `CourseAssignment`. `CourseAssignment` не требуется выделенный первичный ключ. Свойства `InstructorID` и `CourseID` выступают в качестве составного первичного ключа. Единственным способом указать составные первичные ключи для EF Core является *текучий API*. Следующий раздел описывает, как настроить составной первичный ключ.

Составной ключ обеспечивает следующее:

* Для одного курса допускается несколько строк.
* Для одного преподавателя допускается несколько строк.
* Несколько строк для одного преподавателя и курса недопустимы.

Сущность соединения `Enrollment` определяет собственный первичный ключ, поэтому подобные дубликаты возможны. Для предотвращения подобных дубликатов:

* добавьте уникальный индекс для полей внешнего ключа или
* настройте `Enrollment` с первичным составным ключом аналогично `CourseAssignment`. Дополнительные сведения см. в разделе [Индексы](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Обновление контекста базы данных

Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Предыдущий код добавляет новые сущности и настраивает составной первичный ключ сущности `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Текучий API вместо атрибутов

Метод `OnModelCreating` в предыдущем коде использует для настройки поведения EF Core *текучий API*. Этот API называется "текучим", так как часто используется для объединения серии вызовов методов в один оператор. В [следующем коде](/ef/core/modeling/#use-fluent-api-to-configure-a-model) показан пример текучего API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

В этом учебнике текучий API используется только для сопоставления базы данных, которое невозможно выполнить с помощью атрибутов. Однако текучий API позволяет задать большинство правил форматирования, проверки и сопоставления, которые можно указать с помощью атрибутов.

Некоторые атрибуты, такие как `MinimumLength`, невозможно применить с текучим API. `MinimumLength` не изменяет схему, он лишь применяет правило проверки минимальной длины.

Некоторые разработчики предпочитают использовать текучий API монопольно, чтобы оставить свои классы сущностей "чистыми". Атрибуты и текучий API можно смешивать. Существует несколько конфигураций, которые можно реализовать только с помощью текучего API (с указанием составного первичного ключа). Существует несколько конфигураций, которые можно реализовать только с помощью атрибутов (`MinimumLength`). Рекомендации по использованию текучего API и атрибутов:

* Выберите один из двух этих подходов.
* Используйте выбранный подход максимально согласованно.

Некоторые атрибуты из этого руководства используются:

* только для проверки (например, `MinimumLength`);
* только для конфигурации EF Core (например, `HasKey`);
* для проверки и конфигурации EF Core (например, `[StringLength(50)]`).

Дополнительные сведения о сравнении атрибутов и текучего API см. в разделе [Методы конфигурации](/ef/core/modeling/).

## <a name="entity-diagram"></a>Схема сущностей

Ниже показана схема, создаваемая средствами EF Power Tools для завершенной модели School.

![Схема сущностей](complex-data-model/_static/diagram.png)

На предыдущей схеме показано следующее:

* Несколько линий связи один ко многим (1 к \*).
* Линия связи один к нулю или одному (1 к 0..1) между сущностями `Instructor` и `OfficeAssignment`.
* Линия связи нуль или один ко многим (0..1 к *) между сущностями `Instructor` и `Department`.

## <a name="seed-the-database"></a>Заполнение базы данных

Обновите код в файле *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Предыдущий код предоставляет начальные данные для новых сущностей. Основная часть кода создает объекты сущностей и загружает демонстрационные данные. Демонстрационные данные используются для проверки. См. `Enrollments` и `CourseAssignments`, чтобы ознакомиться с примерами заполнения данными таблиц соединения "многие ко многим".

## <a name="add-a-migration"></a>Добавление миграции

Выполните построение проекта.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В PMC выполните следующую команду:

```powershell
Add-Migration ComplexDataModel
```

Предыдущая команда отображает предупреждение о возможной потере данных.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

При выполнении команды `database update` возникает следующая ошибка:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

В следующем разделе вы узнаете, что делать с этой ошибкой.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Если добавить миграцию и выполнить команду `database update`, произойдет следующая ошибка:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

В следующем разделе вы узнаете, как ее избежать.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Применение миграции либо удаление и повторное создание

Теперь у вас есть база данных, и пора подумать о том, как применять к ней изменения. В этом руководстве показано два подхода:

* [Удаление и повторное создание базы данных](#drop). Выберите этот раздел, если вы используете SQLite.
* [Применение миграции к существующей базе данных](#applyexisting). Инструкции в этом разделе подходят только для SQL Server, но **не для SQLite**. 

Для SQL Server применимы оба подхода. Хотя метод с применением миграции является более сложным и трудоемким, в реальной рабочей среде лучше использовать именно его. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Удаление и повторное создание базы данных

[Пропустите этот раздел](#apply-the-migration), если вы используете SQL Server и хотите применить миграцию в следующем разделе.

Чтобы в EF Core принудительно создать базу данных, удалить, а затем обновить ее, выполните указанные ниже действия.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выполните следующие команды в **консоли диспетчера пакетов** (PMC):

  ```powershell
  Drop-Database
  ```

* Удалите папку *Migrations*, а затем выполните следующую команду:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте командное окно и перейдите в папку проекта. Папка проекта содержит файл *ContosoUniversity.csproj*.

* Выполните следующую команду:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* Удалите папку *Migrations*, а затем выполните следующую команду:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Запустите приложение. При запуске приложения выполняется метод `DbInitializer.Initialize`. `DbInitializer.Initialize` заполняет новую базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Откройте базу данных в SSOX.

* Если SSOX был открыт ранее, нажмите кнопку **Обновить**.
* Разверните узел **Таблицы**. Отображаются созданные таблицы.

  ![Таблицы в SSOX](complex-data-model/_static/ssox-tables.png)

* Изучите таблицу **CourseAssignment**:

  * Щелкните правой кнопкой мыши таблицу **CourseAssignment** и выберите пункт **Просмотреть данные**.
  * Убедитесь, что таблица **CourseAssignment** содержит данные.

  ![Данные CourseAssignment в SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Используйте средство SQLite для просмотра базы данных:

* новые таблицы и столбцы;
* заполненные данные в таблицах, например в таблице **CourseAssignment**.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Применение миграции

Этот раздел является необязательным. Эти действия подходят только для SQL Server LocalDB и только в том случае, если вы пропустили предыдущий раздел [Удаление и повторное создание базы данных](#drop).

При выполнении миграций с существующими данными могут действовать ограничения внешнего ключа, которым существующие данные не соответствуют. Для рабочих данных нужно предпринять меры по переносу существующих данных. Этот раздел содержит пример того, как устранить нарушения ограничений внешнего ключа. Вносите эти изменения кода только после создания резервной копии. Не вносите эти изменения в код, если вы выполнили инструкции из предыдущего раздела [Удаление и повторное создание базы данных](#drop).

Файл *{метка_времени}_ComplexDataModel.cs* содержит следующий код:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Предыдущий код добавляет в таблицу `Course` внешний ключ `DepartmentID`, не допускающий значение null. База данных из предыдущего учебника содержит строки в `Course`, поэтому эту таблицу невозможно обновить с помощью миграций.

Чтобы заставить миграцию `ComplexDataModel` работать с существующими данными:

* Изменить код, чтобы присвоить новому столбцу (`DepartmentID`) значение по умолчанию.
* Создайте фиктивную кафедру с именем "Temp" для использования по умолчанию.

#### <a name="fix-the-foreign-key-constraints"></a>Устранение ограничений внешнего ключа

В классе миграции `Up` обновите метод `ComplexDataModel`:

* Откройте файл *{метка_времени}_ComplexDataModel.cs*.
* Закомментируйте строку кода, которая добавляет столбец `DepartmentID` в таблицу `Course`.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Добавьте выделенный ниже код. Новый код идет после блока `.CreateTable( name: "Department"`.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

После внесения описанных выше изменений существующие строки `Course` будут связаны с кафедрой "Temp" после выполнения метода `ComplexDataModel.Up`.

Инструкции на случай описанной здесь ситуации упрощены в этом учебнике. Рабочее приложение:

* включает код или сценарии, чтобы добавить строки `Department` и связанные строки `Course` к новым строкам `Department`;
* не использует кафедру "Temp" или значение по умолчанию для `Course.DepartmentID`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выполните следующие команды в **консоли диспетчера пакетов** (PMC):

  ```powershell
  Update-Database
  ```

Так как метод `DbInitializer.Initialize` предназначен для работы только с пустой базой данных, используйте SSOX для удаления всех строк в таблицах Student и Course. (К таблице Enrollment будет применено каскадное удаление.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Если вы используете SQL Server LocalDB с Visual Studio Code, выполните следующую команду:

  ```dotnetcli
  dotnet ef database update
  ```

---

Запустите приложение. При запуске приложения выполняется метод `DbInitializer.Initialize`. `DbInitializer.Initialize` заполняет новую базу данных.

## <a name="next-steps"></a>Следующие шаги

В следующих двух учебниках рассказывается о том, как считывать и обновлять связанные данные.

> [!div class="step-by-step"]
> [Предыдущий учебник](xref:data/ef-rp/migrations)
> [Следующий учебник](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Предыдущие руководства работали с базовой моделью данных, состоящей из трех сущностей. В этом руководстве:

* Добавляются дополнительные сущности и связи.
* Настраивается модель данных с помощью указания правил для форматирования, проверки и сопоставления базы данных.

Классы сущностей для готовой модели данных показаны на следующем рисунке:

![Схема сущностей](complex-data-model/_static/diagram.png)

При возникновении проблем, которые вам не удается устранить, скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Настройка модели данных с использованием атрибутов

В этом разделе модель данных настраивается с помощью атрибутов.

### <a name="the-datatype-attribute"></a>Атрибут DataType

Страницы учащихся сейчас отображают время и дату зачисления. Как правило, поля даты отображают только дату без времени.

Обновите *Models/Student.cs*, используя следующий выделенный код:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Атрибут [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) указывает тип данных более точно по сравнению со встроенным типом базы данных. В этом случае следует отображать отобразить только дату, а не дату и время. В [перечислении DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) представлено множество типов данных, таких как Date, Time, PhoneNumber, Currency, EmailAddress и других. Атрибут `DataType` также обеспечивает автоматическое предоставление функций для определенных типов в приложении. Пример:

* Ссылка `mailto:` для `DataType.EmailAddress` создается автоматически.
* Средство выбора даты предоставляется для `DataType.Date` в большинстве браузеров.

Атрибут `DataType` создает атрибуты HTML 5 `data-`, которые используются браузерами с поддержкой HTML 5. Атрибуты `DataType` не предназначены для проверки.

`DataType.Date` не задает формат отображаемой даты. По умолчанию поле даты отображается с использованием форматов, установленных в [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) сервера.

С помощью атрибута `DisplayFormat` можно явно указать формат даты:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Параметр `ApplyFormatInEditMode` указывает, что формат должен применяться к пользовательскому интерфейсу редактирования. Некоторым полям не следует использовать `ApplyFormatInEditMode`. Например, обозначение денежной единицы в общем случае не должно отображаться в поле редактирования текста.

Атрибут `DisplayFormat` можно использовать отдельно. Однако чаще всего `DataType` рекомендуется применять вместе с атрибутом `DisplayFormat`. Атрибут `DataType` передает семантику данных (в отличие от способа их вывода на экран). Атрибут `DataType` дает следующие преимущества, недоступные в `DisplayFormat`:

* Поддержка функций HTML5 в браузере. Например, отображение элемента управления календарем, соответствующего языковому стандарту обозначения денежной единицы, ссылок электронной почты, проверки входных данных на стороне клиента и т. д.
* По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.

Дополнительные сведения см. в документации по [вспомогательной функции тегов \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).

Запустите приложение. Перейдите на страницу индекса учащихся. Время больше не отображается. Каждое представление, использующее модель `Student`, отображает дату без времени.

![Страница указателя учащихся с датами без времени](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Атрибут StringLength

С помощью атрибутов можно указать правила проверки данных и сообщения об ошибках проверки. Атрибут [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) указывает минимальную и максимальную длину символов, разрешенных в поле данных. Атрибут `StringLength` также обеспечивает проверку на стороне клиента и на стороне сервера. Минимальное значение не оказывает влияния на схему базы данных.

Обновите модель`Student`, используя следующий код:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Предыдущий код задает ограничение длины имен в 50 символов. Атрибут `StringLength` не запрещает пользователю ввести пробел в качестве имени пользователя. Атрибут [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) используется для применения ограничений к входным данным. Например, следующий код требует, чтобы первый символ был прописной буквой, а остальные символы были буквенными:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

Запустите приложение:

* Перейдите на страницу учащихся.
* Выберите **Create New** (Создать) и введите имя длиной более 50 символов.
* Выберите **Create** (Создать), проверка на стороне клиента отображает сообщение об ошибке.

![Страница указателя учащихся с ошибками длины строки](complex-data-model/_static/string-length-errors.png)

В **обозревателе объектов SQL Server** (SSOX) откройте конструктор таблиц учащихся, дважды щелкнув таблицу **Student** (Учащийся).

![Таблица учащихся в SSOX до миграций](complex-data-model/_static/ssox-before-migration.png)

На предыдущем изображении показана схемы для таблицы `Student`. Поля имен имеют тип `nvarchar(MAX)`, так как миграции для базы данных не выполнялись. Когда в дальнейшем миграции будут выполнены, поля имен станут `nvarchar(50)`.

### <a name="the-column-attribute"></a>Атрибут Column

Атрибуты могут управлять, как именно классы и свойства сопоставляются с базой данных. В этом разделе атрибут `Column` используется для сопоставления имени свойства `FirstMidName` с "FirstName" в базе данных.

При создании базы данных имена свойств в модели используются для имен столбцов (кроме случая, когда используется атрибут `Column`).

Модель `Student` использует `FirstMidName` для поля имени, так как это поле также может содержать отчество.

Обновите файл *Student.cs*, используя следующий выделенный код:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

С учетом предыдущего изменения `Student.FirstMidName` в приложении сопоставляется со столбцом `FirstName` таблицы `Student`.

Добавление атрибута `Column` изменяет модель для поддержки `SchoolContext`. Модель, поддерживающая `SchoolContext`, больше не соответствует базе данных. Если приложение запускается перед применением миграций, возникает следующее исключение:

```
SqlException: Invalid column name 'FirstName'.
```

Для обновления базы данных сделайте следующее:

* Выполните построение проекта.
* Откройте командное окно в папке проекта. Введите следующие команды для создания миграции и обновления базы данных:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

Команда `migrations add ColumnFirstName` выдает следующее предупреждающее сообщение:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Это предупреждение вызвано тем, что поля имен теперь ограничены 50 символами. Если имя в базе данных имеет больше 50 символов, символы с 51 до последнего будут потеряны.

* Проверьте работу приложения.

Откройте таблицу "Student" (Учащийся) в окне SSOX:

![Таблица учащихся в SSOX после миграций](complex-data-model/_static/ssox-after-migration.png)

До применения миграции столбцы имен имели тип [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Теперь столбцы имен имеют тип `nvarchar(50)`. Имя столбца изменилось с `FirstMidName` на `FirstName`.

> [!Note]
> В следующем разделе сборка приложения на некоторых этапах приводит к возникновению ошибок компилятора. В инструкциях указано, когда производить сборку приложения.

## <a name="student-entity-update"></a>Обновление сущности Student

![Сущность Student](complex-data-model/_static/student-entity.png)

Обновите *Models/Student.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Атрибут Required

Атрибут `Required` делает свойства имен обязательными полями. Атрибут `Required` не нужен для типов, не допускающих значения null, например для типов значений (`DateTime`, `int`, `double` и т. д.). Типы, которые не могут принимать значение null, автоматически обрабатываются как обязательные поля.

Атрибут `Required` можно заменить параметром минимальной длины в атрибуте `StringLength`:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Атрибут Display

Атрибут `Display` указывает, что заголовки для текстовых полей должны иметь вид "First Name" (Имя), "Last Name" (Фамилия), "Full Name" (Полное имя) и "Enrollment Date" (Дата зачисления). По умолчанию в заголовках не использовался пробел для разделения слов, например "Lastname".

### <a name="the-fullname-calculated-property"></a>Вычисляемое свойство FullName

`FullName` — это вычисляемое свойство, которое возвращает значение, созданное путем объединения двух других свойств. `FullName` не может быть задано, оно имеет только метод доступа get. В базе данных не создается столбец `FullName`.

## <a name="create-the-instructor-entity"></a>Создание сущности Instructor

![Сущность Instructor](complex-data-model/_static/instructor-entity.png)

Создайте файл *Models/Instructor.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

На одной строке могут находиться несколько атрибутов. Атрибуты `HireDate` можно записать следующим образом:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Свойства навигации CourseAssignments и OfficeAssignment

`CourseAssignments` и `OfficeAssignment` — это свойства навигации.

Преподаватель может проводить любое количество курсов, поэтому `CourseAssignments` определен как коллекция.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Если свойство навигации содержит несколько сущностей:

* Оно должно быть типом списка, где можно добавлять, удалять и обновлять записи.

К типам свойств навигации относятся следующие:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Если указан тип `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.

Сущность `CourseAssignment` описана в разделе по связям многие ко многим.

Бизнес-правила университета Contoso указывают, что преподаватель может иметь не более одного кабинета. Свойство `OfficeAssignment` содержит отдельную сущность `OfficeAssignment`. `OfficeAssignment` имеет значение null, если кабинет не назначен.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Создание сущности OfficeAssignment

![Сущность OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Создайте файл *Models/OfficeAssignment.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Атрибут Key

Атрибут `[Key]` используется для идентификации свойства в качестве первичного ключа (PK), когда имя свойства отличается от classnameID или ID.

Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному. Назначение кабинета существует только в связи с преподавателем, которому оно назначено. Первичный ключ `OfficeAssignment` также является внешним ключом (FK) для сущности `Instructor`. EF Core не распознает автоматически `InstructorID` как первичный ключ объекта `OfficeAssignment` по следующей причине:

* `InstructorID` не соблюдает соглашение об именовании ID или classnameID.

Таким образом, атрибут `Key` используется для определения `InstructorID` в качестве первичного ключа:

```csharp
[Key]
public int InstructorID { get; set; }
```

По умолчанию EF Core считает ключ созданным не базой данных, так как столбец предназначен для идентифицирующего отношения.

### <a name="the-instructor-navigation-property"></a>Свойство навигации Instructor

Свойство навигации `OfficeAssignment` для сущности `Instructor` допускает значение null по следующим причинам:

* Ссылочные типы (например, классы) допускают значение null.
* Преподавателю может быть не назначен кабинет.

Сущность `OfficeAssignment` имеет свойство навигации `Instructor`, не допускающее значение null, по следующим причинам:

* `InstructorID` не допускает значение null.
* Назначение кабинета не может существовать без преподавателя.

Когда сущность `Instructor` имеет связанную сущность `OfficeAssignment`, каждая из них имеет ссылку на другую в своем свойстве навигации.

Атрибут `[Required]` можно применить к свойству навигации `Instructor`:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Предыдущий код указывает, что должен существовать связанный преподаватель. Этот код не нужен, так как внешний ключ `InstructorID` (который также является первичным) не допускает значение null.

## <a name="modify-the-course-entity"></a>Изменение сущности Course

![Сущность Course](complex-data-model/_static/course-entity.png)

Обновите *Models/Course.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Сущность `Course` имеет свойство внешнего ключа (FK) `DepartmentID`. `DepartmentID` указывает на связанную сущность `Department`. Сущность `Course` имеет свойство навигации `Department`.

EF Core не требует свойство внешнего ключа для модели данных, если она имеет свойство навигации для связанной сущности.

EF Core автоматически создает внешние ключи в базе данных по мере необходимости. EF Core создает [теневые свойства](/ef/core/modeling/shadow-properties) для автоматически созданных внешних ключей. Наличие внешнего ключа в модели данных позволяет сделать обновления проще и эффективнее. Например, рассмотрим модель, где свойство внешнего ключа `DepartmentID`*не* включено. При получении сущности курса для редактирования:

* сущность `Department` имеет значение null, если она не загружена явно;
* для обновления сущности курса нужно сначала получить сущность `Department`.

Если свойство внешнего ключа `DepartmentID` включено в модель данных, получать сущность `Department` перед обновлением не нужно.

### <a name="the-databasegenerated-attribute"></a>Атрибут DatabaseGenerated

Атрибут `[DatabaseGenerated(DatabaseGeneratedOption.None)]` указывает, что первичный ключ предоставляется приложением, а не создается базой данных.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

По умолчанию EF Core предполагает, что значения первичного ключа создаются базой данных. Обычно лучше всего использовать значения первичного ключа, созданные базой данных. Для сущностей `Course` пользователь указывает первичный ключ. Например, номер курса, такой как серия 1000 для кафедры математики и серия 2000 для кафедры английского языка.

Атрибут `DatabaseGenerated` также может использоваться для создания значений по умолчанию. Например, база данных может автоматически создать поле даты для записи даты, когда строка была создана или изменена. Дополнительные сведения см. в разделе [Созданные свойства](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа (FK) и свойства навигации в сущности `Course` отражают следующие связи:

Курс назначается одной кафедре, поэтому имеется внешний ключ `DepartmentID` и свойство навигации `Department`.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

На курс может быть зачислено любое количество учащихся, поэтому свойство навигации `Enrollments` является коллекцией:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Курс могут вести несколько преподавателей, поэтому свойство навигации `CourseAssignments` является коллекцией:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` описано [далее](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Создание сущности Department

![Сущность Department](complex-data-model/_static/department-entity.png)

Создайте файл *Models/Department.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Атрибут Column

Ранее атрибут `Column` использовался, чтобы изменить сопоставление имени столбца. В коде для сущности `Department` атрибут `Column` можно использовать, чтобы изменить сопоставление типов данных SQL. Столбец `Budget` определяется с помощью типа money SQL Server в базе данных:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Сопоставление столбцов обычно не требуется. В общем случае EF Core выбирает соответствующий тип данных SQL Server на основе типа среды CLR для свойства. Тип `decimal` среды CLR сопоставляется с типом `decimal` SQL Server. `Budget` используется для денежных единиц, хотя для этого лучше подходит тип данных money.

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа и навигации отражают следующие связи:

* Кафедра может иметь или не иметь администратора.
* Администратор всегда является преподавателем. Поэтому свойство `InstructorID` включается в качестве внешнего ключа в сущность `Instructor`.

Свойство навигации называется `Administrator`, но содержит сущность `Instructor`:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Вопросительный знак (?) в приведенном выше коде указывает, что свойство допускает значение null.

Кафедра может иметь несколько курсов, поэтому доступно свойство навигации Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

Примечание. По соглашению EF Core разрешает каскадное удаление для внешних ключей, не допускающих значение null, и связей "многие ко многим". Каскадное удаление может привести к циклическим правилам каскадного удаления. Такие правила вызывают исключение при добавлении миграции.

Например, если свойство `Department.InstructorID` было определено как не допускающее значения NULL:

* EF Core настраивает правило каскадного удаления для удаления кафедры при удалении преподавателя.
* Удаление кафедры при удалении преподавателя не является запланированным поведением.
* Следующий [текучий API](#fluent-api-alternative-to-attributes) будет задавать правило ограничения вместо каскада.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Предыдущий код отключает каскадное удаление для связи кафедры и преподавателя.

## <a name="update-the-enrollment-entity"></a>Обновление сущности Enrollment

Запись зачисления обозначает один курс, который проходит один учащийся.

![Сущность Enrollment](complex-data-model/_static/enrollment-entity.png)

Обновите *Models/Enrollment.cs*, используя следующий код:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Свойства внешнего ключа и навигации

Свойства внешнего ключа и навигации отражают следующие связи:

Запись зачисления предназначена для одного курса, поэтому доступно свойство первичного ключа `CourseID` и свойство навигации `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Запись зачисления предназначена для одного учащегося, поэтому доступно свойство первичного ключа `StudentID` и свойство навигации `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Связи многие ко многим

Между сущностями `Student` и `Course` действует связь многие ко многим. Сущности `Enrollment` выступают в качестве таблицы соединения многие ко многим *с полезными данными* в базе данных. Фраза "с полезными данными" означает, что таблица `Enrollment` содержит дополнительные данные, кроме внешних ключей для присоединяемых таблиц (в данном случае — первичный ключ и `Grade`).

На следующем рисунке показано, как выглядят эти связи на схеме сущностей. (Эта схема была создана с помощью [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) для EF 6.x. Создание схемы не является частью руководства.)

![Связь многие ко многим между Student и Course](complex-data-model/_static/student-course.png)

Каждая линия связи имеет 1 на одном конце и звездочку (*) на другом, указывая характер один ко многим.

Если таблица `Enrollment` не включала в себя сведения об оценках, ей потребуется содержать всего два внешних ключа (`CourseID` и `StudentID`). Таблицу соединения многие ко многим без полезных данных иногда называют чистой таблицей соединения (PJT).

Сущности `Instructor` и `Course` имеют связь многие ко многим с использованием чистой таблицы соединения.

Примечание. EF 6.x поддерживает неявные таблицы соединения для связей многие ко многим, но EF Core — нет. Дополнительные сведения см. в статье [Связи многие ко многим в EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Сущность CourseAssignment

![Сущность CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Создайте файл *Models/CourseAssignment.cs* со следующим кодом:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Связь между Instructor и Courses

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

Связь многие ко многим между Instructor и Courses:

* Нуждается в таблице соединения, которая должна быть представлена набором сущностей.
* Является чистой таблицей соединения (таблицей без полезных данных).

Обычно для сущности соединения используется имя `EntityName1EntityName2`. Например, таблицей соединения Instructor и Courses, использующей этот шаблон, является `CourseInstructor`. Однако рекомендуется использовать имя, которое описывает эту связь.

Модели данных создаются простыми и разрастаются. Соединения без полезных данных (PJT) часто начинают включать их. Если в начале задать описательное имя сущности, его не нужно менять при изменениях таблицы соединения. Оптимально, если сущность соединения имеет собственное естественное имя (возможно, из одного слова) в бизнес-среде. Например, Books и Customers можно связать с сущностью соединения Ratings. Связь многие ко многим между Instructor и Courses `CourseAssignment` является более предпочтительным вариантом, чем `CourseInstructor`.

### <a name="composite-key"></a>Составной ключ

Внешние ключи не допускают значение null. Два внешних ключа в `CourseAssignment` (`InstructorID` и `CourseID`) совместно однозначно определяют каждую строку таблицы `CourseAssignment`. `CourseAssignment` не требуется выделенный первичный ключ. Свойства `InstructorID` и `CourseID` выступают в качестве составного первичного ключа. Единственным способом указать составные первичные ключи для EF Core является *текучий API*. Следующий раздел описывает, как настроить составной первичный ключ.

Составной ключ обеспечивает следующее:

* Для одного курса допускается несколько строк.
* Для одного преподавателя допускается несколько строк.
* Несколько строк для одного преподавателя и курса недопустимы.

Сущность соединения `Enrollment` определяет собственный первичный ключ, поэтому подобные дубликаты возможны. Для предотвращения подобных дубликатов:

* добавьте уникальный индекс для полей внешнего ключа или
* настройте `Enrollment` с первичным составным ключом аналогично `CourseAssignment`. Дополнительные сведения см. в разделе [Индексы](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Изменение контекста базы данных

Добавьте выделенный ниже код в файл *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Предыдущий код добавляет новые сущности и настраивает составной первичный ключ сущности `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Текучий API вместо атрибутов

Метод `OnModelCreating` в предыдущем коде использует для настройки поведения EF Core *текучий API*. Этот API называется "текучим", так как часто используется для объединения серии вызовов методов в один оператор. В [следующем коде](/ef/core/modeling/#use-fluent-api-to-configure-a-model) показан пример текучего API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

В этом руководстве текучий API используется только для сопоставления базы данных, которое невозможно выполнить с помощью атрибутов. Однако текучий API позволяет задать большинство правил форматирования, проверки и сопоставления, которые можно указать с помощью атрибутов.

Некоторые атрибуты, такие как `MinimumLength`, невозможно применить с текучим API. `MinimumLength` не изменяет схему, он лишь применяет правило проверки минимальной длины.

Некоторые разработчики предпочитают использовать текучий API монопольно, чтобы оставить свои классы сущностей "чистыми". Атрибуты и текучий API можно смешивать. Существует несколько конфигураций, которые можно реализовать только с помощью текучего API (с указанием составного первичного ключа). Существует несколько конфигураций, которые можно реализовать только с помощью атрибутов (`MinimumLength`). Рекомендации по использованию текучего API и атрибутов:

* Выберите один из двух этих подходов.
* Используйте выбранный подход максимально согласованно.

Некоторые атрибуты из этого руководства используются:

* только для проверки (например, `MinimumLength`);
* только для конфигурации EF Core (например, `HasKey`);
* для проверки и конфигурации EF Core (например, `[StringLength(50)]`).

Дополнительные сведения о сравнении атрибутов и текучего API см. в разделе [Методы конфигурации](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Схема сущностей, показывающая связи

Ниже показана схема, создаваемая средствами EF Power Tools для завершенной модели School.

![Схема сущностей](complex-data-model/_static/diagram.png)

На предыдущей схеме показано следующее:

* Несколько линий связи один ко многим (1 к \*).
* Линия связи один к нулю или одному (1 к 0..1) между сущностями `Instructor` и `OfficeAssignment`.
* Линия связи нуль или один ко многим (0..1 к *) между сущностями `Instructor` и `Department`.

## <a name="seed-the-db-with-test-data"></a>Заполнение базы данных тестовыми данными

Обновите код в файле *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Предыдущий код предоставляет начальные данные для новых сущностей. Основная часть кода создает объекты сущностей и загружает демонстрационные данные. Демонстрационные данные используются для проверки. См. `Enrollments` и `CourseAssignments`, чтобы ознакомиться с примерами заполнения данными таблиц соединения "многие ко многим".

## <a name="add-a-migration"></a>Добавление миграции

Выполните построение проекта.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Предыдущая команда отображает предупреждение о возможной потере данных.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

При выполнении команды `database update` возникает следующая ошибка:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Применение миграции

Теперь у вас есть база данных, и пора подумать о том, как применять к ней будущие изменения. В этом руководстве показано два подхода:

* [Удаление и повторное создание базы данных](#drop)
* [Применение миграции к существующей базе данных](#applyexisting). Хотя этот метод является более сложным и трудоемким, в реальной рабочей среде лучше использовать именно его. **Примечание**. Это необязательный раздел руководства. Вы можете выполнить удаление и повторное создание и пропустить этот раздел. Если вы хотите выполнить инструкции в этом разделе, не выполняйте удаление и повторное создание. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Удаление и повторное создание базы данных

Код в обновленном `DbInitializer` предоставляет начальные данные для новых сущностей. Чтобы выполнить в EF Core принудительное создание новой базы данных, удаление и обновление базы данных:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующие команды в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database
Update-Database
```

Чтобы просмотреть справочную информацию, выполните команду `Get-Help about_EntityFrameworkCore` в PMC.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Откройте командное окно и перейдите в папку проекта. Папка проекта содержит файл *Startup.cs*.

Введите в командном окне следующее:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Запустите приложение. При запуске приложения выполняется метод `DbInitializer.Initialize`. `DbInitializer.Initialize` заполняет новую базу данных.

Откройте базу данных в SSOX:

* Если SSOX был открыт ранее, нажмите кнопку **Обновить**.
* Разверните узел **Таблицы**. Отображаются созданные таблицы.

![Таблицы в SSOX](complex-data-model/_static/ssox-tables.png)

Изучите таблицу **CourseAssignment**:

* Щелкните правой кнопкой мыши таблицу **CourseAssignment** и выберите пункт **Просмотреть данные**.
* Убедитесь, что таблица **CourseAssignment** содержит данные.

![Данные CourseAssignment в SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Применение миграции к существующей базе данных

Этот раздел является необязательным. Эти действия подходят только в том случае, если вы пропустили предыдущий раздел [Удаление и повторное создание базы данных](#drop).

При выполнении миграций с существующими данными могут действовать ограничения внешнего ключа, которым существующие данные не соответствуют. Для рабочих данных нужно предпринять меры по переносу существующих данных. Этот раздел содержит пример того, как устранить нарушения ограничений внешнего ключа. Вносите эти изменения кода только после создания резервной копии. Не вносите эти изменения кода, если вы выполнили инструкции из предыдущего раздела и обновили базу данных.

Файл *{метка_времени}_ComplexDataModel.cs* содержит следующий код:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Предыдущий код добавляет в таблицу `Course` внешний ключ `DepartmentID`, не допускающий значение null. База данных из предыдущего руководства содержит строки в `Course`, поэтому эту таблицу невозможно обновить с помощью миграций.

Чтобы заставить миграцию `ComplexDataModel` работать с существующими данными:

* Изменить код, чтобы присвоить новому столбцу (`DepartmentID`) значение по умолчанию.
* Создайте фиктивную кафедру с именем "Temp" для использования по умолчанию.

#### <a name="fix-the-foreign-key-constraints"></a>Устранение ограничений внешнего ключа

Обновите метод `Up` в классах `ComplexDataModel`.

* Откройте файл *{метка_времени}_ComplexDataModel.cs*.
* Закомментируйте строку кода, которая добавляет столбец `DepartmentID` в таблицу `Course`.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Добавьте выделенный ниже код. Новый код идет после блока `.CreateTable( name: "Department"`.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

После внесения описанных выше изменений существующие строки `Course` будут связаны с кафедрой "Temp" после выполнения метода `ComplexDataModel` `Up`.

Рабочее приложение:

* включает код или сценарии, чтобы добавить строки `Department` и связанные строки `Course` к новым строкам `Department`;
* не использует кафедру "Temp" или значение по умолчанию для `Course.DepartmentID`.

Следующее руководство посвящено связанным данным.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube (часть 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Версия руководства на YouTube (часть 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Назад](xref:data/ef-rp/migrations)
> [Вперед](xref:data/ef-rp/read-related-data)

::: moniker-end
