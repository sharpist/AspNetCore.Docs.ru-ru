---
title: Часть 8. Добавление нового поля в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 8 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d2b3b22a94e3119712e331565cc74ffa60ada726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050710"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="027ab-103">Часть 8. Добавление нового поля в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="027ab-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="027ab-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="027ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="027ab-105">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="027ab-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="027ab-106">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="027ab-106">Add a new field to the model.</span></span>
* <span data-ttu-id="027ab-107">Перенос нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="027ab-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="027ab-108">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="027ab-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="027ab-109">Добавляет таблицу в базу данных для отслеживания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="027ab-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="027ab-110">Проверяет, синхронизирована ли база данных с классами модели, из которых она была создана.</span><span class="sxs-lookup"><span data-stu-id="027ab-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="027ab-111">Если синхронизация нарушена, EF вызывает исключение.</span><span class="sxs-lookup"><span data-stu-id="027ab-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="027ab-112">Это позволяет упростить поиск проблем с согласованностью между базой данных и кодом.</span><span class="sxs-lookup"><span data-stu-id="027ab-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="027ab-113">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="027ab-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="027ab-114">Добавьте свойство `Rating` в *Models/Movie.cs* :</span><span class="sxs-lookup"><span data-stu-id="027ab-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="027ab-115">Сборка приложения</span><span class="sxs-lookup"><span data-stu-id="027ab-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="027ab-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="027ab-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="027ab-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="027ab-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="027ab-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="027ab-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="027ab-119">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="027ab-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="027ab-120">Command ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="027ab-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="027ab-121">Поскольку в класс `Movie` было добавлено новое поле, необходимо обновить список привязки свойств, включив в него новое свойство.</span><span class="sxs-lookup"><span data-stu-id="027ab-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="027ab-122">В файле *MoviesController.cs* обновите атрибут `[Bind]` для методов действия `Create` и `Edit`, включив свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="027ab-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="027ab-123">Обновите шаблоны представлений, чтобы реализовать отображение, создание и редактирование нового свойства `Rating` в представлении браузера.</span><span class="sxs-lookup"><span data-stu-id="027ab-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="027ab-124">Измените файл */Views/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="027ab-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="027ab-125">Обновите файл */Views/Movies/Create.cshtml* , указав поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="027ab-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="027ab-126">Visual Studio / Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="027ab-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="027ab-127">Вы можете скопировать и вставить предыдущую "группу форм" и дождаться автоматического обновления полей с помощью IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="027ab-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="027ab-128">IntelliSense работает со [вспомогательными функциями тегов](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="027ab-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Разработчик ввел букву R в качестве значения атрибута asp-for во втором элементе label представления.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="027ab-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="027ab-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="027ab-133">Обновите остальные шаблоны.</span><span class="sxs-lookup"><span data-stu-id="027ab-133">Update the remaining templates.</span></span>

<span data-ttu-id="027ab-134">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="027ab-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="027ab-135">Ниже показан пример изменения, которое необходимо выполнить для каждого `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="027ab-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="027ab-136">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="027ab-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="027ab-137">Если он запущена, возникает следующее исключение `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="027ab-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="027ab-138">Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в существующей базе данных.</span><span class="sxs-lookup"><span data-stu-id="027ab-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="027ab-139">(В таблице базы данных отсутствует столбец `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="027ab-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="027ab-140">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="027ab-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="027ab-141">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="027ab-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="027ab-142">Этот подход удобен на ранних стадиях цикла разработки, когда все действия осуществляются с тестовой базой данных. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="027ab-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="027ab-143">Недостатком такого подхода является потеря существующих данных в базе, в связи с чем использовать его в рабочей базе данных невозможно.</span><span class="sxs-lookup"><span data-stu-id="027ab-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="027ab-144">При разработке приложения часто используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="027ab-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="027ab-145">Это хороший подход на ранних этапах разработки и при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="027ab-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="027ab-146">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="027ab-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="027ab-147">Преимущество такого подхода состоит в том, что сохраняются все данные.</span><span class="sxs-lookup"><span data-stu-id="027ab-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="027ab-148">Это изменение можно выполнить как вручную, так и с помощью соответствующего скрипта базы данных.</span><span class="sxs-lookup"><span data-stu-id="027ab-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="027ab-149">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="027ab-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="027ab-150">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="027ab-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="027ab-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="027ab-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="027ab-152">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="027ab-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Меню PMC](adding-model/_static/pmc.png)

<span data-ttu-id="027ab-154">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="027ab-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="027ab-155">Команда `Add-Migration` указывает платформе миграции на необходимость проверить текущую модель `Movie` с текущей схемой базы данных `Movie` и создать нужный код для переноса базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="027ab-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="027ab-156">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="027ab-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="027ab-157">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="027ab-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="027ab-158">Если удалить все записи из базы данных, при инициализации она будет заполнена значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="027ab-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="027ab-159">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="027ab-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="027ab-160">Удалите базу данных и предыдущую миграцию, а затем используйте функцию миграций для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="027ab-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="027ab-161">Для удаления последней миграции используйте `dotnet ef migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="027ab-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="027ab-162">При наличии нескольких миграций, удалите папку Migrations.</span><span class="sxs-lookup"><span data-stu-id="027ab-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="027ab-163">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="027ab-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="027ab-164">[Назад](search.md)
> [Вперед](validation.md)</span><span class="sxs-lookup"><span data-stu-id="027ab-164">[Previous](search.md)
[Next](validation.md)</span></span>
