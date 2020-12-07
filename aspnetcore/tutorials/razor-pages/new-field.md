---
title: Часть 7. Добавление нового поля
author: rick-anderson
description: Часть 7 серии руководств по Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 6b6856731c61957a9e23f76e2bc15befe56ea57d
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420010"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="1f40a-103">Часть 7. Добавление нового поля на страницу Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f40a-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="1f40a-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1f40a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1f40a-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1f40a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1f40a-106">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="1f40a-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="1f40a-107">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-107">Add a new field to the model.</span></span>
* <span data-ttu-id="1f40a-108">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="1f40a-109">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="1f40a-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="1f40a-110">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="1f40a-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="1f40a-111">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="1f40a-112">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="1f40a-113">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="1f40a-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="1f40a-114">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="1f40a-115">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="1f40a-115">Build the app.</span></span>

1. <span data-ttu-id="1f40a-116">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="1f40a-117">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-117">Update the following pages:</span></span>
   1. <span data-ttu-id="1f40a-118">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="1f40a-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="1f40a-119">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="1f40a-120">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="1f40a-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="1f40a-121">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="1f40a-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="1f40a-122">При запуске приложения без обновления базы данных возникает `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="1f40a-123">Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="1f40a-124">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="1f40a-125">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="1f40a-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="1f40a-126">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="1f40a-127">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="1f40a-128">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="1f40a-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="1f40a-129">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="1f40a-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="1f40a-130">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1f40a-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="1f40a-131">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="1f40a-132">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="1f40a-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="1f40a-133">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="1f40a-134">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="1f40a-135">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="1f40a-136">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="1f40a-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="1f40a-137">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="1f40a-138">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="1f40a-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="1f40a-139">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f40a-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f40a-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="1f40a-141">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="1f40a-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="1f40a-142">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="1f40a-143">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1f40a-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="1f40a-144">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="1f40a-145">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="1f40a-146">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="1f40a-147">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="1f40a-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="1f40a-148">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="1f40a-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="1f40a-149">Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.</span><span class="sxs-lookup"><span data-stu-id="1f40a-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="1f40a-150">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="1f40a-151">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1f40a-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="1f40a-152">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="1f40a-153">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="1f40a-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="1f40a-154">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="1f40a-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="1f40a-155">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="1f40a-156">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="1f40a-157">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-157">Select **OK**.</span></span>
1. <span data-ttu-id="1f40a-158">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="1f40a-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1f40a-159">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1f40a-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="1f40a-160">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="1f40a-161">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="1f40a-162">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="1f40a-163">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="1f40a-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="1f40a-164">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="1f40a-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="1f40a-165">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="1f40a-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="1f40a-166">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="1f40a-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="1f40a-167">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="1f40a-168">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="1f40a-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="1f40a-169">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="1f40a-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="1f40a-170">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-170">Creating a new table.</span></span>
>* <span data-ttu-id="1f40a-171">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="1f40a-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="1f40a-172">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-172">Dropping the old table.</span></span>
>* <span data-ttu-id="1f40a-173">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-173">Renaming the new table.</span></span>
>
><span data-ttu-id="1f40a-174">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="1f40a-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="1f40a-175">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="1f40a-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="1f40a-176">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="1f40a-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="1f40a-177">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="1f40a-178">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="1f40a-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="1f40a-179">Удалите папку миграции.</span><span class="sxs-lookup"><span data-stu-id="1f40a-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="1f40a-180">Используйте следующие команды, чтобы заново создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="1f40a-181">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="1f40a-182">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f40a-183">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f40a-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1f40a-184">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="1f40a-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="1f40a-185">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1f40a-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1f40a-186">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="1f40a-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="1f40a-187">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-187">Add a new field to the model.</span></span>
* <span data-ttu-id="1f40a-188">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="1f40a-189">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="1f40a-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="1f40a-190">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="1f40a-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="1f40a-191">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="1f40a-192">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="1f40a-193">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="1f40a-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="1f40a-194">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="1f40a-195">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="1f40a-195">Build the app.</span></span>

1. <span data-ttu-id="1f40a-196">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="1f40a-197">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-197">Update the following pages:</span></span>
   1. <span data-ttu-id="1f40a-198">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="1f40a-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="1f40a-199">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="1f40a-200">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="1f40a-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="1f40a-201">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="1f40a-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="1f40a-202">При запуске приложения без обновления базы данных возникает `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="1f40a-203">Исключение `SqlException` связано с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="1f40a-204">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="1f40a-205">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="1f40a-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="1f40a-206">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="1f40a-207">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="1f40a-208">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="1f40a-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="1f40a-209">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="1f40a-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="1f40a-210">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1f40a-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="1f40a-211">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="1f40a-212">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="1f40a-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="1f40a-213">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="1f40a-214">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="1f40a-215">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="1f40a-216">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="1f40a-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="1f40a-217">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="1f40a-218">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="1f40a-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="1f40a-219">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f40a-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f40a-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="1f40a-221">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="1f40a-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="1f40a-222">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="1f40a-223">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1f40a-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="1f40a-224">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="1f40a-225">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="1f40a-226">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="1f40a-227">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="1f40a-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="1f40a-228">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="1f40a-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="1f40a-229">Команда `Update-Database` указывает платформе, что к базе данных нужно применить изменения схемы, а также сохранить существующие данные.</span><span class="sxs-lookup"><span data-stu-id="1f40a-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="1f40a-230">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="1f40a-231">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1f40a-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="1f40a-232">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="1f40a-233">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="1f40a-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="1f40a-234">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="1f40a-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="1f40a-235">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="1f40a-236">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="1f40a-237">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-237">Select **OK**.</span></span>
* <span data-ttu-id="1f40a-238">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="1f40a-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1f40a-239">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1f40a-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="1f40a-240">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="1f40a-241">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="1f40a-242">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="1f40a-243">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="1f40a-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="1f40a-244">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="1f40a-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="1f40a-245">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="1f40a-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="1f40a-246">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="1f40a-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="1f40a-247">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="1f40a-248">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="1f40a-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="1f40a-249">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="1f40a-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="1f40a-250">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-250">Creating a new table.</span></span>
>* <span data-ttu-id="1f40a-251">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="1f40a-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="1f40a-252">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-252">Dropping the old table.</span></span>
>* <span data-ttu-id="1f40a-253">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-253">Renaming the new table.</span></span>
>
><span data-ttu-id="1f40a-254">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="1f40a-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="1f40a-255">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="1f40a-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="1f40a-256">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="1f40a-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="1f40a-257">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="1f40a-258">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="1f40a-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="1f40a-259">Удалите папку миграции.</span><span class="sxs-lookup"><span data-stu-id="1f40a-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="1f40a-260">Используйте следующие команды, чтобы заново создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="1f40a-261">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="1f40a-262">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f40a-263">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f40a-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1f40a-264">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="1f40a-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f40a-265">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1f40a-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1f40a-266">В этом разделе [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations используется для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="1f40a-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="1f40a-267">Добавление нового поля в модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-267">Add a new field to the model.</span></span>
* <span data-ttu-id="1f40a-268">Перенос изменений в схеме нового поля в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="1f40a-269">Если вы используете EF Code First для автоматического создания базы данных, Code First:</span><span class="sxs-lookup"><span data-stu-id="1f40a-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="1f40a-270">Добавляет в базу данных таблицу [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table), которая позволяет отслеживать синхронизацию схемы базы данных с классами модели, на основе которой она была создана.</span><span class="sxs-lookup"><span data-stu-id="1f40a-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="1f40a-271">Если классы модели не синхронизированы с базой данных, EF выдает исключение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="1f40a-272">Автоматическая проверка синхронизации схемы и модели упрощает поиск несогласованных проблем в коде базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="1f40a-273">Добавление свойства Rating в модель Movie</span><span class="sxs-lookup"><span data-stu-id="1f40a-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="1f40a-274">Откройте файл *Models/Movie.cs* и добавьте свойство `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="1f40a-275">Построение приложения.</span><span class="sxs-lookup"><span data-stu-id="1f40a-275">Build the app.</span></span>

<span data-ttu-id="1f40a-276">Измените файл *Pages/Movies/Index.cshtml* и добавьте поле `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="1f40a-277">Обновите следующие страницы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-277">Update the following pages:</span></span>

* <span data-ttu-id="1f40a-278">Добавьте поле `Rating` на страницы "Delete" (Удаление) и "Details" (Сведения).</span><span class="sxs-lookup"><span data-stu-id="1f40a-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="1f40a-279">Обновите файл [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml), добавив в него поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="1f40a-280">Добавьте поле `Rating` на страницу "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="1f40a-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="1f40a-281">Для работы приложения необходимо обновить базу данных, включив в нее новое поле.</span><span class="sxs-lookup"><span data-stu-id="1f40a-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="1f40a-282">Если приложение выполняется, возникнет исключение `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="1f40a-283">Эта ошибка связана с тем, что обновленный класс модели Movie отличается от схемы таблицы Movie в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="1f40a-284">В таблице базы данных нет столбца `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="1f40a-285">Устранить эту ошибку можно несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="1f40a-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="1f40a-286">Можно с помощью Entity Framework автоматически удалить и повторно создать базу данных на основе новой схемы класса модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="1f40a-287">Этот подход удобен на ранних стадиях цикла разработки. В этом случае развитие модели и схемы базы данных осуществляется одновременно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="1f40a-288">Недостатком такого подхода является потеря существующих данных в базе.</span><span class="sxs-lookup"><span data-stu-id="1f40a-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="1f40a-289">В рабочей базе данных применять этот подход не следует!</span><span class="sxs-lookup"><span data-stu-id="1f40a-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="1f40a-290">При разработке приложения часто выполняется удаление базы данных при изменении схемы, для чего используется инициализатор для автоматического заполнения базы тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1f40a-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="1f40a-291">Можно явно изменить схему существующей базы данных в соответствии с новыми классами модели.</span><span class="sxs-lookup"><span data-stu-id="1f40a-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="1f40a-292">Преимущество подхода в том, что он сохраняет данные.</span><span class="sxs-lookup"><span data-stu-id="1f40a-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="1f40a-293">Внесите это изменение вручную либо путем создания скрипта изменения для базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="1f40a-294">Можно обновить схему базы данных с помощью Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="1f40a-295">В этом руководстве используется Code First Migrations.</span><span class="sxs-lookup"><span data-stu-id="1f40a-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="1f40a-296">Обновите класс `SeedData` так, чтобы он предоставлял значение нового столбца.</span><span class="sxs-lookup"><span data-stu-id="1f40a-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="1f40a-297">Ниже приведен пример изменения, которое необходимо реализовать для каждого блока `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="1f40a-298">См. [готовый файл SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="1f40a-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="1f40a-299">Постройте решение.</span><span class="sxs-lookup"><span data-stu-id="1f40a-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f40a-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f40a-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="1f40a-301">Добавление миграции для поля Rating</span><span class="sxs-lookup"><span data-stu-id="1f40a-301">Add a migration for the rating field</span></span>

<span data-ttu-id="1f40a-302">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="1f40a-303">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1f40a-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="1f40a-304">Команда `Add-Migration` задает следующие инструкции для платформы:</span><span class="sxs-lookup"><span data-stu-id="1f40a-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="1f40a-305">Сравните модель `Movie` со схемой базы данных `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="1f40a-306">Создайте код для переноса схемы базы данных в новую модель.</span><span class="sxs-lookup"><span data-stu-id="1f40a-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="1f40a-307">В качестве имени файла переноса используется произвольное имя "Rating".</span><span class="sxs-lookup"><span data-stu-id="1f40a-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="1f40a-308">Рекомендуется присваивать этому файлу понятное имя.</span><span class="sxs-lookup"><span data-stu-id="1f40a-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="1f40a-309">Команда `Update-Database` указывает платформе, что нужно применить изменения схемы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="1f40a-310">Если удалить все записи из базы данных, при инициализации она будет заполнена начальными значениями и в нее будет включено поле `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="1f40a-311">Это можно сделать с помощью ссылок удаления в браузере или из [обозревателя объектов SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1f40a-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="1f40a-312">Другой вариант — удалить базу данных и использовать миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="1f40a-313">Удаление базы данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="1f40a-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="1f40a-314">Выберите базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="1f40a-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="1f40a-315">Щелкните базу данных правой кнопкой мыши и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="1f40a-316">Выберите **Закрыть существующие соединения**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="1f40a-317">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1f40a-317">Select **OK**.</span></span>
* <span data-ttu-id="1f40a-318">Обновите базу данных в [PMC](xref:tutorials/razor-pages/new-field#pmc).</span><span class="sxs-lookup"><span data-stu-id="1f40a-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1f40a-319">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1f40a-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="1f40a-320">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="1f40a-321">В этом руководстве используется функция *миграции* Entity Framework Core, где это возможно.</span><span class="sxs-lookup"><span data-stu-id="1f40a-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="1f40a-322">Во время миграции обновляется схема базы данных в соответствии с изменениями в модели данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="1f40a-323">Но миграции могут вносить только изменения, которые поддерживает поставщик EF Core, а возможности поставщика SQLite ограничены.</span><span class="sxs-lookup"><span data-stu-id="1f40a-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="1f40a-324">Например, добавление столбца поддерживается, но удаление или изменение столбца не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="1f40a-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="1f40a-325">Если миграция создается для удаления или изменения столбца, команда `ef migrations add` выполняется успешно, а команда `ef database update` — нет.</span><span class="sxs-lookup"><span data-stu-id="1f40a-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="1f40a-326">Из-за этих ограничений в этом руководстве не используются миграции для изменения схемы SQLite.</span><span class="sxs-lookup"><span data-stu-id="1f40a-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="1f40a-327">Вместо этого при изменении схемы нужно удалить и снова создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="1f40a-328">Обходной путь для ограничений SQLite — вручную написать код миграции для перестроения таблицы в случае изменений.</span><span class="sxs-lookup"><span data-stu-id="1f40a-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="1f40a-329">Перестроение таблицы включает в себя:</span><span class="sxs-lookup"><span data-stu-id="1f40a-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="1f40a-330">Создание новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-330">Creating a new table.</span></span>
>* <span data-ttu-id="1f40a-331">Копирование данных из старой таблицы в новую.</span><span class="sxs-lookup"><span data-stu-id="1f40a-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="1f40a-332">Удаление старой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-332">Dropping the old table.</span></span>
>* <span data-ttu-id="1f40a-333">Переименование новой таблицы.</span><span class="sxs-lookup"><span data-stu-id="1f40a-333">Renaming the new table.</span></span>
>
><span data-ttu-id="1f40a-334">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="1f40a-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="1f40a-335">Ограничения поставщика базы данных SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="1f40a-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="1f40a-336">Настройка кода миграции</span><span class="sxs-lookup"><span data-stu-id="1f40a-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="1f40a-337">Присвоение начальных значений данных</span><span class="sxs-lookup"><span data-stu-id="1f40a-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="1f40a-338">Инструкция по ALTER TABLE SQLite</span><span class="sxs-lookup"><span data-stu-id="1f40a-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="1f40a-339">Удалите базу данных и используйте миграции для повторного создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1f40a-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="1f40a-340">Чтобы удалить базу данных, удалите файл базы данных (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="1f40a-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="1f40a-341">Затем выполните команду `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="1f40a-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="1f40a-342">Запустите приложение и проверьте возможность создания, редактирования и отображения фильмов с использованием поля `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="1f40a-343">Если база данных не заполнена начальными значениями, задайте точку останова в методе `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="1f40a-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f40a-344">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f40a-344">Additional resources</span></span>

* [<span data-ttu-id="1f40a-345">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="1f40a-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="1f40a-346">[Предыдущая статья. Добавление поиска](xref:tutorials/razor-pages/search)
> [Следующая статья. Добавление проверки](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="1f40a-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
