---
title: Часть 8. Добавление нового поля в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 8 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 2dfaba166617c5d0a224f4b47028c141e59ce47f
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652911"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4b774-103">Часть 8. Добавление нового поля в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b774-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4b774-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="4b774-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b774-105">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="4b774-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4b774-106">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="4b774-106">Add a new field to the model.</span></span>
* <span data-ttu-id="4b774-107">Перенос нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="4b774-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="4b774-108">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="4b774-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4b774-109">Добавляет таблицу в базу данных для отслеживания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="4b774-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="4b774-110">Проверяет, синхронизирована ли база данных с классами модели, из которых она была создана.</span><span class="sxs-lookup"><span data-stu-id="4b774-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="4b774-111">Если синхронизация нарушена, EF вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="4b774-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="4b774-112">Это позволяет упростить поиск проблем с согласованностью между базой данных и кодом.</span><span class="sxs-lookup"><span data-stu-id="4b774-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4b774-113">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="4b774-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4b774-114">Добавьте свойство `Rating` в *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b774-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4b774-115">Сборка приложения</span><span class="sxs-lookup"><span data-stu-id="4b774-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="4b774-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b774-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="4b774-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="4b774-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4b774-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b774-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b774-119">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="4b774-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b774-120">Command ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="4b774-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="4b774-121">Поскольку в класс `Movie` было добавлено новое поле, необходимо обновить утвержденный список привязки, включив в него новое свойство.</span><span class="sxs-lookup"><span data-stu-id="4b774-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="4b774-122">В файле *MoviesController.cs* обновите атрибут `[Bind]` для методов действия `Create` и `Edit`, включив свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="4b774-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="4b774-123">Обновите шаблоны представлений, чтобы реализовать отображение, создание и редактирование нового свойства `Rating` в представлении браузера.</span><span class="sxs-lookup"><span data-stu-id="4b774-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="4b774-124">Измените файл */Views/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="4b774-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="4b774-125">Обновите файл */Views/Movies/Create.cshtml*, указав поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4b774-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="4b774-126">Visual Studio / Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="4b774-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="4b774-127">Вы можете скопировать и вставить предыдущую "группу форм" и дождаться автоматического обновления полей с помощью IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="4b774-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="4b774-128">IntelliSense работает со [вспомогательными функциями тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4b774-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Разработчик ввел букву R в качестве значения атрибута asp-for во втором элементе label представления.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b774-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b774-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="4b774-133">Обновите остальные шаблоны.</span><span class="sxs-lookup"><span data-stu-id="4b774-133">Update the remaining templates.</span></span>

<span data-ttu-id="4b774-134">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="4b774-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4b774-135">Ниже показан пример изменения, которое необходимо выполнить для каждого `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="4b774-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4b774-136">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="4b774-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="4b774-137">Если он запущена, возникает следующее исключение `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="4b774-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4b774-138">Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в существующей базе данных.</span><span class="sxs-lookup"><span data-stu-id="4b774-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="4b774-139">(В таблице базы данных отсутствует столбец `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="4b774-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="4b774-140">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="4b774-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4b774-141">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="4b774-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="4b774-142">Этот подход удобен на ранних стадиях цикла разработки, когда все действия осуществляются с тестовой базой данных. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="4b774-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4b774-143">Недостатком такого подхода является потеря существующих данных в базе, в связи с чем использовать его в рабочей базе данных невозможно.</span><span class="sxs-lookup"><span data-stu-id="4b774-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="4b774-144">При разработке приложения часто используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="4b774-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="4b774-145">Это хороший подход на ранних этапах разработки и при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="4b774-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="4b774-146">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="4b774-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4b774-147">Преимущество такого подхода состоит в том, что сохраняются все данные.</span><span class="sxs-lookup"><span data-stu-id="4b774-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="4b774-148">Это изменение можно выполнить как вручную, так и с помощью соответствующего скрипта базы данных.</span><span class="sxs-lookup"><span data-stu-id="4b774-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4b774-149">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="4b774-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4b774-150">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="4b774-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b774-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b774-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b774-152">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="4b774-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Меню PMC](adding-model/_static/pmc.png)

<span data-ttu-id="4b774-154">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="4b774-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4b774-155">Команда `Add-Migration` указывает платформе миграции на необходимость проверить текущую модель `Movie` с текущей схемой базы данных `Movie` и создать нужный код для переноса базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="4b774-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="4b774-156">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="4b774-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4b774-157">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="4b774-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4b774-158">Если удалить все записи из базы данных, при инициализации она будет заполнена значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4b774-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b774-159">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="4b774-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="4b774-160">Удалите базу данных и используйте миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="4b774-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4b774-161">Чтобы удалить базу данных, удалите файл базы данных (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="4b774-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="4b774-162">Затем выполните команду `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="4b774-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="4b774-163">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4b774-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span> <span data-ttu-id="4b774-164">Обновите приложение.</span><span class="sxs-lookup"><span data-stu-id="4b774-164">Update the app:</span></span>

* <span data-ttu-id="4b774-165">Добавьте поле `Rating` в шаблоны представлений `Edit`, `Details` и `Delete`.</span><span class="sxs-lookup"><span data-stu-id="4b774-165">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>
* <span data-ttu-id="4b774-166">Обновите привязку в методе действия изменения контроллера `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="4b774-166">Update the binding in the edit action method of the `MoviesController`.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4b774-167">[Назад](search.md)
> [Вперед](validation.md)</span><span class="sxs-lookup"><span data-stu-id="4b774-167">[Previous](search.md)
[Next](validation.md)</span></span>
