---
title: Часть 8. Добавление нового поля в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 8 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d2b3b22a94e3119712e331565cc74ffa60ada726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050710"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>Часть 8. Добавление нового поля в приложение MVC ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:

* Добавление нового поля в модель.
* Перенос нового поля в базу данных.

Если вы используете EF Code First для автоматического создания базы данных, Code First:

* Добавляет таблицу в базу данных для отслеживания схемы базы данных.
* Проверяет, синхронизирована ли база данных с классами модели, из которых она была создана. Если синхронизация нарушена, EF вызывает исключение. Это позволяет упростить поиск проблем с согласованностью между базой данных и кодом.

## <a name="add-a-rating-property-to-the-movie-model"></a>Добавление свойства Rating в модель Movie

Добавьте свойство `Rating` в *Models/Movie.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Сборка приложения

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Command ⌘ + B

------

Поскольку в класс `Movie` было добавлено новое поле, необходимо обновить список привязки свойств, включив в него новое свойство. В файле *MoviesController.cs* обновите атрибут `[Bind]` для методов действия `Create` и `Edit`, включив свойство `Rating`:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Обновите шаблоны представлений, чтобы реализовать отображение, создание и редактирование нового свойства `Rating` в представлении браузера.

Измените файл */Views/Movies/Index.cshtml* и добавьте поле `Rating`:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Обновите файл */Views/Movies/Create.cshtml* , указав поле `Rating`.

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio / Visual Studio для Mac](#tab/visual-studio+visual-studio-mac)

Вы можете скопировать и вставить предыдущую "группу форм" и дождаться автоматического обновления полей с помощью IntelliSense. IntelliSense работает со [вспомогательными функциями тегов](xref:mvc/views/tag-helpers/intro).

![Разработчик ввел букву R в качестве значения атрибута asp-for во втором элементе label представления. Появились контекстные меню Intellisense, в которых представлены доступные поля, в том числе Rating, выделяемое в списке автоматически. Если разработчик щелкнет поле или нажмет клавишу ВВОД, полю Rating будет присвоено значение.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Обновите остальные шаблоны.

Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца. Ниже показан пример изменения, которое необходимо выполнить для каждого `new Movie`.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Для работы приложения необходимо обновить базу данных, включив в нее новое поле. Если он запущена, возникает следующее исключение `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в существующей базе данных. (В таблице базы данных отсутствует столбец `Rating`.)

Устранить эту ошибку можно несколькими способами:

1. Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели. Этот подход удобен на ранних стадиях цикла разработки, когда все действия осуществляются с тестовой базой данных. В этом случае развитие модели и схемы базы данных осуществляется одновременно. Недостатком такого подхода является потеря существующих данных в базе, в связи с чем использовать его в рабочей базе данных невозможно. При разработке приложения часто используется инициализатор для автоматического заполнения базы тестовыми данными. Это хороший подход на ранних этапах разработки и при использовании SQLite.

2. Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели. Преимущество такого подхода состоит в том, что сохраняются все данные. Это изменение можно выполнить как вручную, так и с помощью соответствующего скрипта базы данных.

3. Можно обновить схему базы данных с помощью Code First Migrations.

В этом руководстве используется Code First Migrations.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.

  ![Меню PMC](adding-model/_static/pmc.png)

В PMC введите следующие команды:

```powershell
Add-Migration Rating
Update-Database
```

Команда `Add-Migration` указывает платформе миграции на необходимость проверить текущую модель `Movie` с текущей схемой базы данных `Movie` и создать нужный код для переноса базы данных в новую модель.

В качестве имени файла переноса используется произвольное имя "Rating". Рекомендуется присваивать этому файлу понятное имя.

Если удалить все записи из базы данных, при инициализации она будет заполнена значениями и в нее будет включено поле `Rating`.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Удалите базу данных и предыдущую миграцию, а затем используйте функцию миграций для повторного создания базы данных.

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

Для удаления последней миграции используйте `dotnet ef migrations remove`. При наличии нескольких миграций, удалите папку Migrations.

---
<!-- End of VS tabs -->

Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.

> [!div class="step-by-step"]
> [Назад](search.md)
> [Вперед](validation.md)
