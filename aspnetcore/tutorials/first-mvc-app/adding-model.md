---
title: Часть 4. Добавление модели в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 4 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 29f70d6bd1d5c1223ef35b4e24e5b9c0a8465d1d
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652383"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>Часть 4. Добавление модели в приложение MVC ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)

В этом разделе мы добавим классы для управления фильмами в базе данных. Эти классы будут представлять уровень **м**одели в приложении **M**VC.

Эти классы используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных. EF Core —это платформа объектно реляционного сопоставления (ORM), которая позволяет упростить необходимый код доступа к данным.

Создаваемые вами классы моделей называются классами POCO (от **P**lain **O**ld **C**LR **O** — старые добрые объекты CLR), так как они не зависят от EF Core. Эти классы просто определяют свойства данных, которые будут храниться в базе данных.

Работая с этим учебником, вы напишете классы моделей, а затем EF Core создаст базу данных.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Добавление класса модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**. Назовите файл *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Добавьте файл *Movie.cs* в папку *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**. Назовите файл *Movie.cs*.

---

Обновите файл *Movie.cs*, используя следующий код:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.

Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`). С этим атрибутом:

* пользователю не требуется вводить сведения о времени в поле даты.
* Отображается только дата, а не время.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.

## <a name="add-nuget-packages"></a>Добавление пакетов NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

В PMC выполните следующую команду:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Приведенная выше команда добавляет поставщик EF Core для SQL Server. Пакет поставщика устанавливает пакет EF Core в качестве зависимости. Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

В меню **Проект** выберите **Управление пакетами NuGet**.

В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**. Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Откроется диалоговое окно **Выбор проектов**, в котором будет выбран проект `MvcMovie`. Нажмите кнопку **ОК**.

Появится диалоговое окно **Принятие условий лицензионного соглашения**. Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.

Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Создание класса контекста для базы данных

Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`. Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.

Создайте папку *Data*.

Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Регистрация контекста базы данных

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве. В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.

Добавьте следующие инструкции `using` в начало файла *Startup.cs*.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Добавьте выделенный ниже код в `Startup.ConfigureServices`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Добавление строки подключения базы данных

Добавьте строку подключения в файл *appsettings.json*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Выполните сборку проекта, чтобы проверить его на ошибки компиляции.

## <a name="scaffold-movie-pages"></a>Формирования шаблона страниц фильмов

Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.

![представление указанного выше шага](adding-model/_static/add_controller21.png)

В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

Выполните необходимые действия в диалоговом окне **Добавление контроллера**:

* **Класс модели:** *Movie (MvcMovie.Models)*
* **Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*

![Добавление контекста данных](adding-model/_static/dc3.png)

* **Представления:** оставьте флажки, установленные по умолчанию
* **Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)
* Нажмите **Добавить**

Visual Studio создаст следующие компоненты:

* контроллер фильмов (*Controllers/MoviesController.cs*);
* файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).

Автоматическое создание этих файлов называется *формированием шаблонов*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).

* В Linux экспортируйте путь к средству формирования шаблонов:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).

* Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Сформированные страницы пока использовать нельзя, так как база данных не существует. Если запустить приложение и щелкнуть ссылку **Movie App**, появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.

<a name="migration"></a>

## <a name="initial-migration"></a>Первоначальная миграция

Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core. Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).

В PMC введите следующие команды:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*. Аргумент `InitialCreate` — это имя миграции. Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию. Так как это первая миграция, созданный класс содержит код для создания схемы базы данных. Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.

* `Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой. Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.

  Команда обновления базы данных выдает следующее предупреждающее сообщение: 

  > "Для десятичного столбца Price в типе сущности Movie не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".

  Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Выполните следующие команды интерфейса командной строки .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*. Аргумент `InitialCreate` — это имя миграции. Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию. Так как это первая миграция, созданный класс содержит код для создания схемы базы данных. Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).

* `ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой. Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>Класс InitialCreate

Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа. Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и щелкните ссылку **Movie App**.

  Если возникнет исключение наподобие следующего:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  возможно, вы пропустили [шаг миграции](#migration).

* Протестируйте страницу **создания**. Введите и отправьте данные.

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте страницы **редактирования**, **сведений** и **удаления**.

## <a name="dependency-injection-in-the-controller"></a>Внедрение зависимостей в контроллере

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Использование SQLite для среды разработки и SQL Server для рабочей среды

Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке. В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки. Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Строго типизированные модели и ключевое слово @model

Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`. Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.

Модель MVC также поддерживает передачу строго типизированных объектов модели в представление. Такой подход обеспечивает проверку кода во время компиляции. В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.

Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Параметр `id` обычно передается в качестве данных маршрута. Например, `https://localhost:5001/movies/details/1` задает:

* Контроллер `movies` (первый сегмент URL-адреса).
* Действие `details` (второй сегмент URL-адреса).
* Идентификатор 1 (последний сегмент URL-адреса).

Также можно передать `id` с помощью строки запроса следующим образом:

`https://localhost:5001/movies/details?id=1`

Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.

[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:

```csharp
return View(movie);
```

Изучите содержимое файла *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением. При создании контроллера movie был включен следующий оператор `@model`:

```cshtml
@model MvcMovie.Models.Movie
```

Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление. Объект `Model` является строго типизированным. Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`. Методы `Create` и `Edit` и представления также передают объект модели `Movie`.

Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies. Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`. Код передает список `Movies` из метода действия `Index` в представление:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`. Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`. Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro)
* [Глобализация и локализация](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Добавление класса модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**. Присвойте классу имя **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

* Добавьте класс в папку *Models* с именем *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.

![представление указанного выше шага](adding-model/_static/add_controller21.png)

В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

Выполните необходимые действия в диалоговом окне **Добавление контроллера**:

* **Класс модели:** *Movie (MvcMovie.Models)*
* **Класс контекста данных:** щелкните значок **+** и добавьте класс **MvcMovie.Models.MvcMovieContext** по умолчанию.

![Добавление контекста данных](adding-model/_static/dc.png)

* **Представления:** оставьте флажки, установленные по умолчанию
* **Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)
* Нажмите **Добавить**

![Диалоговое окно "Добавление контроллера"](adding-model/_static/add_controller2.png)

Visual Studio создаст следующие компоненты:

* [класс контекста базы данных](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*);
* контроллер фильмов (*Controllers/MoviesController.cs*);
* файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).

Автоматическое создание контекста базы данных и методов и представлений действий [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется *формированием шаблонов*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).
* Установка средства формирования шаблонов:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* В Linux экспортируйте путь к средству формирования шаблонов:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Выполните следующую команду:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).
* Установка средства формирования шаблонов:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Выполните следующую команду:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Если запустить приложение и щелкнуть ссылку **Mvc Movie**, возникает ошибка наподобие следующей:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Вам необходимо создать базу данных. Для этого вы используете функцию [миграций](xref:data/ef-mvc/migrations) EF Core. С помощью миграций можно создать базу данных, соответствующую модели данных, и обновлять схему базы данных при изменении модели.

<a name="pmc"></a>

## <a name="initial-migration"></a>Первоначальная миграция

В этом разделе выполняются следующие задачи:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).

   ![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. В PMC введите следующие команды:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Команда `Add-Migration` формирует код для создания схемы исходной базы данных.

   Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`. Аргумент `Initial` — это имя миграции. Можно использовать любое имя, но по соглашению используется имя, которое описывает миграцию. Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.

   Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs*, который создает базу данных.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Команда `ef migrations add InitialCreate` формирует код для создания схемы исходной базы данных.

Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*). Аргумент `InitialCreate` — это имя миграции. Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Рассмотрим следующий метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`. Контекст данных (`MvcMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст данных указывает сущности, которые включаются в модель данных:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Вы создаете контекст базы данных и регистрируете его с использованием контейнера внедрения зависимостей.

---

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

Если вы получите исключение базы данных, аналогичное следующему:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Вы пропустили [шаг миграции](#pmc).

* Протестируйте ссылку **Создать**. Введите и отправьте данные.

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

Проверьте класс `Startup`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Выделенный выше код демонстрирует добавление контекста базы данных фильмов в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection):

* `services.AddDbContext<MvcMovieContext>(options =>` задает используемую базу данных и строку подключения.
* `=>` — это [лямбда-оператор](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер. Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Строго типизированные модели и ключевое слово @model

Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`. Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.

Модель MVC также поддерживает передачу строго типизированных объектов модели в представление. Такой подход обеспечивает более эффективную проверку кода во время компиляции. При создании методов и представлений в этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классами `MoviesController`.

Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Параметр `id` обычно передается в качестве данных маршрута. Например, `https://localhost:5001/movies/details/1` задает:

* Контроллер `movies` (первый сегмент URL-адреса).
* Действие `details` (второй сегмент URL-адреса).
* Идентификатор 1 (последний сегмент URL-адреса).

Также можно передать `id` с помощью строки запроса следующим образом:

`https://localhost:5001/movies/details?id=1`

Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.

[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:

```csharp
return View(movie);
   ```

Изучите содержимое файла *Views/Movies/Details.cshtml*:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Указав оператор `@model` в начале файла представления, вы можете задать тип объекта, который будет ожидаться представлением. При создании контроллера movie следующий оператор `@model` был автоматически добавлен в начало файла *Details.cshtml*:

```cshtml
@model MvcMovie.Models.Movie
```

Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление с использованием строго типизированного объекта `Model`. Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`. Методы `Create` и `Edit` и представления также передают объект модели `Movie`.

Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies. Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`. Код передает список `Movies` из метода действия `Index` в представление:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

При создании контроллера movies механизм формирования шаблонов автоматически включает следующий оператор `@model` в начало файла *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`. Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`. Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции:

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro)
* [Глобализация и локализация](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Назад: добавление представления](adding-view.md)
> [Далее: работа с базой данных](working-with-sql.md)

::: moniker-end
