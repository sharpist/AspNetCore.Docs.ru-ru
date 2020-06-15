---
title: Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core
author: rick-anderson
description: Часть 2 серии руководств по Razor Pages.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: d3f7d355625ce20a0cb45c785fbab259a5655c63
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652945"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

В этом разделе добавляются классы для управления фильмами. Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных. EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.

Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core. Эти классы определяют свойства данных, которые хранятся в базе данных.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Добавление модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

Щелкните правой кнопкой мыши папку *Models*. Выберите **Добавить** > **Класс**. Присвойте классу имя **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.
* Добавьте класс в папку *Models* с именем *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* На панели решений щелкните проект **RazorPagesMovie** правой кнопкой мыши, выберите пункт **Добавить**>**Новая папка**. Присвойте папке имя *Models*.
* Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**
* В диалоговом окне **Новый файл** выполните следующие действия.

  * Выберите на левой панели пункт **Общие**.
  * В центральной области выберите **Пустой класс**.
  * Назовите класс **Movie** и выберите **Создать**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить** > **Новая папка**.
* Назовите папку *Movies*

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/sca.png)

В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .
* В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie.**Models**.RazorPagesMovieContext на RazorPagesMovie.**Data**.RazorPagesMovieContext. [Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется. Оно создает класс контекста базы данных с правильным пространством имен.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).
* Установка средства формирования шаблонов:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Для Windows**: Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Для macOS и Linux**: Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить** > **Новая папка**.
* Назовите папку *Movies*

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить** > **New Scaffolding...** (Создать шаблон...).

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .
* В строке **Data context class** (Класс контекста данных) введите имя нового класса RazorPagesMovie.**Data**.RazorPagesMovieContext. [Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется. Оно создает класс контекста базы данных с правильным пространством имен.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

### <a name="add-ef-tools"></a>Добавление средств EF

Выполните следующую команду .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI.

---

### <a name="files-created"></a>Создаваемые файлы

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Обновленные возможности

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

В процессе формирования шаблонов создаются указанные ниже файлы.

* *Pages/Movies*: Create, Delete, Details, Edit и Index.

В следующем разделе приводится описание созданных файлов.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Первоначальная миграция

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

В PMC введите следующие команды:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".

Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.

Команда миграции формирует код для создания схемы исходной базы данных. Схема создается на основе модели, указанной в `DbContext`. Аргумент `InitialCreate` используется для присвоения имен миграциям. Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.

Команда `update` выполняет метод `Up` в миграциях, которые не были применены. В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`. Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст данных указывает сущности, которые включаются в модель данных.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Проверьте метод `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Проверьте метод `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

Если возникает ошибка.

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Вы пропустили [шаг миграции](#pmc).

* Протестируйте ссылку **Создать**.

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html). Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite. Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных. EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.

Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core. Эти классы определяют свойства данных, которые хранятся в базе данных.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Добавление модели данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**. Присвойте папке имя *Models*.

Щелкните правой кнопкой мыши папку *Models*. Выберите **Добавить** > **Класс**. Присвойте классу имя **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Добавьте папку с именем *Models*.
* Добавьте класс в папку *Models* с именем *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* В обозревателе решений щелкните проект **RazorPagesMovie** правой кнопкой мыши, выберите пункт **Добавить** > **Новая папка**. Присвойте папке имя *Models*.
* Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл**.
* В диалоговом окне **Новый файл** выполните следующие действия.

  * Выберите на левой панели пункт **Общие**.
  * В центральной области выберите **Пустой класс**.
  * Назовите класс **Movie** и выберите **Создать**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.

## <a name="scaffold-the-movie-model"></a>Создание модели фильма

В этом разделе создается модель фильма. То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить** > **Новая папка**.
* Назовите папку *Movies*

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/sca.png)

В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .
* В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arp.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).

* **Для Windows**: Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Для macOS и Linux**: Выполните следующую команду:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Создайте папку *Pages/Movies*:

* Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить** > **Новая папка**.
* Назовите папку *Movies*

Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :

* В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.
* В строке **Data context class** (Класс контекста данных) введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен. В этом случае это будет **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Нажмите **Добавить**.

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.

---

В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.

### <a name="files-created"></a>Создаваемые файлы

* *Pages/Movies*: Create, Delete, Details, Edit и Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Обновляемые файлы

* *Startup.cs*

В следующем разделе приводится описание созданных и обновленных файлов.

<a name="pmc"></a>

## <a name="initial-migration"></a>Первоначальная миграция

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:

* Добавления первоначальной миграции.
* Обновления базы данных с помощью первоначальной миграции.

В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

В PMC введите следующие команды:

```powershell
Add-Migration Initial
Update-Database
```

Команда `Add-Migration` формирует код для создания схемы исходной базы данных. Схема создается на основе модели, указанной в `DbContext` (в файле *RazorPagesMovieContext.cs*). Аргумент `InitialCreate` используется для присвоения имен миграции. Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию. Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.

Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*. Метод `Up` создает базу данных.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> В результате выполнения предыдущих команд выводится следующее предупреждение: "*Для десятичного столбца "Price" в типе сущности "Movie" не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода "HasColumnType()" явно укажите тип столбца SQL Server, который может вместить все значения.* " Вы можете игнорировать это предупреждение, оно будет исправлено в следующем учебнике.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Проверка контекста, зарегистрированного с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.

Проверьте метод `Startup.ConfigureServices`. Средством формирования шаблонов была добавлена выделенная строка:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`. Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Контекст данных указывает сущности, которые включаются в модель данных.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей. В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных. Сущность соответствует строке в таблице.

Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Проверьте метод `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Проверьте метод `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).

Если возникает ошибка.

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Вы пропустили [шаг миграции](#pmc).

* Протестируйте ссылку **Создать**.

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > В поле `Price` нельзя вводить десятичные запятые. Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения. Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
