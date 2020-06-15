---
title: Часть 4. Работа с базой данных и ASP.NET Core
author: rick-anderson
description: Часть 4 серии руководств по Razor Pages.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 21ae2ed4e91a0b3e52b1cdad1f4f4686c50614ba
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652986"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a>Часть 4. Работа с базой данных и ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Джо Одетт](https://twitter.com/joeaudette) (Joe Audette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных. Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`. Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Значение имени для базы данных (`Database={Database name}`) будет отличаться для созданного кода. Значение имени является произвольным.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к реальному серверу базы данных с помощью переменной среды. Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:\Users\<user>\`.

<a name="ssox"></a>
* В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).

  ![Меню "Вид"](sql/_static/ssox.png)

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**.

  ![Контекстные меню, открытые на таблице Movie](sql/_static/design.png)

  ![Таблицы Movie, открытые в конструкторе](sql/_static/dv.png)

Обратите внимание на значок с изображением ключа рядом с `ID`. По умолчанию EF создает свойство с именем `ID` для первичного ключа.

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**.

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Заполнение базы данных

Создайте класс `SeedData` в папке *Models* со следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Добавление инициализатора заполнения

В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызов метода инициализации с передачей ему контекста.
* Высвобождение контекста после завершения работы метода заполнения.

В следующем примере кода показан обновленный файл *Program.cs*.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Если `Update-Database` не выполнялось, возникает следующее исключение:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Тестирование приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Удалите все записи из базы данных. Это можно сделать с помощью ссылок удаления в браузере или из [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения. Для этого следует остановить и перезапустить IIS Express. Воспользуйтесь одним из перечисленных ниже подходов.

  * Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выход** или **Остановить сайт**.

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * Если среда Visual Studio была запущена в режиме без отладки, нажмите клавишу F5 для запуска в режиме отладки.
    * Если среда Visual Studio была запущена в режиме отладки, остановите отладчик и нажмите клавишу F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Удалите все записи в базе данных для запуска метода заполнения. Остановите и запустите приложение, чтобы начать заполнение базы данных.

В приложении будут отображены данные.

---

В следующем руководстве будет улучшено представление данных.

## <a name="additional-resources"></a>Дополнительные ресурсы

> [!div class="step-by-step"]
> [Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Изменение созданных страниц](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Объект `RazorPagesMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных. Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Дополнительные сведения о методах, которые используются в `ConfigureServices`, см.:

* [Общий регламент по защите данных (GDPR), принятый в ЕС, в ASP.NET Core](xref:security/gdpr) для `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`. Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Значение имени для базы данных (`Database={Database name}`) будет отличаться для созданного кода. Значение имени является произвольным.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к реальному серверу базы данных с помощью переменной среды. Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию база данных LocalDB создает файлы `*.mdf` в каталоге `C:/Users/<user/>`.

<a name="ssox"></a>
* В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).

  ![Меню "Вид"](sql/_static/ssox.png)

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**.

  ![Контекстное меню, открытое на таблице Movie](sql/_static/design.png)

  ![Таблица Movie, открытая в конструкторе](sql/_static/dv.png)

Обратите внимание на значок с изображением ключа рядом с `ID`. По умолчанию EF создает свойство с именем `ID` для первичного ключа.

* Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотреть данные**.

  ![Открытая таблица Movie с отображением данных](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Заполнение базы данных

Создайте класс `SeedData` в папке *Models* со следующим кодом:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Добавление инициализатора заполнения

В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызов метода инициализации с передачей ему контекста.
* Высвобождение контекста после завершения работы метода заполнения.

В следующем примере кода показан обновленный файл *Program.cs*.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Рабочее приложение не вызывает `Database.Migrate`. Он добавляется в предыдущем коде, чтобы предотвратить следующее исключение, если `Update-Database` не был запущен.

SqlException: Не удается открыть базу данных "RazorPagesMovieContext-21", запрашиваемую именем входа. Сбой при входе.
Сбой при входе в систему пользователя user name.

### <a name="test-the-app"></a>Тестирование приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Удалите все записи из базы данных. Это можно сделать с помощью ссылок удаления в браузере или из [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения. Для этого следует остановить и перезапустить IIS Express. Воспользуйтесь одним из перечисленных ниже подходов.

  * Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выйти** или **Остановить сайт**.

    ![Значок IIS Express в области уведомлений](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](sql/_static/stopIIS.png)

    * Если среда Visual Studio была запущена в режиме без отладки, нажмите клавишу F5 для запуска в режиме отладки.
    * Если среда Visual Studio была запущена в режиме отладки, остановите отладчик и нажмите клавишу F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Удалите все записи в базе данных для запуска метода заполнения. Остановите и запустите приложение, чтобы начать заполнение базы данных.

В приложении будут отображены данные.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Удалите все записи в базе данных для запуска метода заполнения. Остановите и запустите приложение, чтобы начать заполнение базы данных.

В приложении будут отображены данные.

---

В приложении отображаются заполненные данные.

![Приложение Movie с данными по фильмам, открытое в Chrome](sql/_static/m55.png)

В следующем учебнике будет улучшено представление данных.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Предыдущая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)
> [Далее: Изменение созданных страниц](xref:tutorials/razor-pages/da1)

::: moniker-end
