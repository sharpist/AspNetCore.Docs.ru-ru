---
title: Часть 4. Razor Pages с EF Core в ASP.NET Core — миграции
author: rick-anderson
description: Часть 4 серии руководств по Razor Pages и Entity Framework.
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
uid: data/ef-rp/migrations
ms.openlocfilehash: 78eb466fcfeb130e411df490f033114b3fdebeef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722635"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="ce64a-103">Часть 4. Миграции Razor Pages с EF Core в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce64a-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="ce64a-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="ce64a-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce64a-105">В этом учебнике представлена функция миграций EF Core для управления изменениями модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="ce64a-106">При разработке нового приложения модель данных часто изменяется.</span><span class="sxs-lookup"><span data-stu-id="ce64a-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="ce64a-107">При каждом изменении нарушается синхронизация модели с базой данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="ce64a-108">Вы начали работу с этой серией учебников с настройки платформы Entity Framework для создания базы данных, если она еще не существует.</span><span class="sxs-lookup"><span data-stu-id="ce64a-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="ce64a-109">Каждый раз при изменении модели данных необходимо удалять базу данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="ce64a-110">При следующем запуске приложения вызов `EnsureCreated` повторно создает базу данных в соответствии с новой моделью данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="ce64a-111">Затем выполняется класс `DbInitializer` для заполнения новой базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="ce64a-112">Этот подход к обеспечению синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="ce64a-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="ce64a-113">Приложение, выполняющееся в рабочей среде, обычно содержит данные.</span><span class="sxs-lookup"><span data-stu-id="ce64a-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="ce64a-114">Приложение не может запускаться с тестовой базой данных каждый раз при внесении изменений (например, при добавлении столбца).</span><span class="sxs-lookup"><span data-stu-id="ce64a-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="ce64a-115">Функция миграций EF Core решает эту проблему, позволяя EF Core обновить схему базы данных вместо создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="ce64a-116">Вместо удаления и повторного создания базы данных при изменении модели функция миграций обновляет схему, сохраняя существующие данные.</span><span class="sxs-lookup"><span data-stu-id="ce64a-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="ce64a-117">Удаление базы данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce64a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce64a-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce64a-119">Используйте **обозреватель объектов SQL Server**, чтобы удалить базу данных, или выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="ce64a-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce64a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce64a-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ce64a-121">Чтобы установить CLI EF, выполните в командной строке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ce64a-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="ce64a-122">В командной строке перейдите к папке проекта.</span><span class="sxs-lookup"><span data-stu-id="ce64a-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="ce64a-123">Папка проекта содержит файл *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="ce64a-124">Удалите файл *CU.db* или выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ce64a-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="ce64a-125">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="ce64a-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce64a-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce64a-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce64a-127">Выполните следующие команды в PMC:</span><span class="sxs-lookup"><span data-stu-id="ce64a-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce64a-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce64a-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ce64a-129">Убедитесь в том, что в командной строке выбрана папка проекта, и выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="ce64a-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="ce64a-130">Методы Up и Down</span><span class="sxs-lookup"><span data-stu-id="ce64a-130">Up and Down methods</span></span>

<span data-ttu-id="ce64a-131">Команда EF Core `migrations add` создала код для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="ce64a-132">Код миграции находится в файле *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="ce64a-133">Метод `Up` класса `InitialCreate` создает таблицы базы данных, соответствующие наборам сущностей модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="ce64a-134">Метод `Down` удаляет их, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ce64a-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="ce64a-135">Приведенный выше код предназначен для первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="ce64a-136">Код.</span><span class="sxs-lookup"><span data-stu-id="ce64a-136">The code:</span></span>

* <span data-ttu-id="ce64a-137">был создан командой `migrations add InitialCreate`;</span><span class="sxs-lookup"><span data-stu-id="ce64a-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="ce64a-138">выполняется командой `database update`;</span><span class="sxs-lookup"><span data-stu-id="ce64a-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="ce64a-139">создает базу данных для модели данных, заданной классом контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="ce64a-140">Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла.</span><span class="sxs-lookup"><span data-stu-id="ce64a-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="ce64a-141">В качестве имени миграции можно использовать любое допустимое имя файла.</span><span class="sxs-lookup"><span data-stu-id="ce64a-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="ce64a-142">Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="ce64a-143">Например, миграция, обеспечивающая добавление таблицы кафедр, может называться "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="ce64a-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="ce64a-144">Таблица журнала миграции</span><span class="sxs-lookup"><span data-stu-id="ce64a-144">The migrations history table</span></span>

* <span data-ttu-id="ce64a-145">Используйте обозреватель объектов SQL Server или средство SQLite для просмотра базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="ce64a-146">Обратите внимание на добавление таблицы `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ce64a-147">Таблица `__EFMigrationsHistory` используется для отслеживания миграций, которые были применены к базе данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="ce64a-148">Просмотрите данные в таблице `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ce64a-149">Вы увидите одну строку для первой миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="ce64a-150">Моментальный снимок модели данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-150">The data model snapshot</span></span>

<span data-ttu-id="ce64a-151">Функция миграций создает *моментальный снимок* текущей модели данных в файле *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="ce64a-152">При добавлении миграции EF определяет, что именно изменилось, сравнивая текущую модель данных с файлом моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="ce64a-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="ce64a-153">Так как файл моментального снимка отслеживает состояние модели данных, вы не можете удалить миграцию, удалив файл `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="ce64a-154">Чтобы отменить последнюю миграцию, необходимо использовать команду `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="ce64a-155">Она удаляет миграцию и гарантирует корректный сброс моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="ce64a-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="ce64a-156">Дополнительные сведения см. в разделе [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="ce64a-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="ce64a-157">Удаление EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="ce64a-157">Remove EnsureCreated</span></span>

<span data-ttu-id="ce64a-158">Эта серия учебников началась с использования `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="ce64a-159">Метод `EnsureCreated` не создает таблицу журнала миграций и поэтому не может использоваться с функцией миграций.</span><span class="sxs-lookup"><span data-stu-id="ce64a-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="ce64a-160">Он предназначен для тестирования или быстрого создания прототипов, когда часто требуется удалять и повторно создавать базу данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="ce64a-161">Начиная с этого момента в учебниках будут использоваться миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="ce64a-162">В файле *Data/DBInitializer.cs* закомментируйте следующую строку:</span><span class="sxs-lookup"><span data-stu-id="ce64a-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="ce64a-163">Запустите приложение и убедитесь в том, что база данных заполняется.</span><span class="sxs-lookup"><span data-stu-id="ce64a-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="ce64a-164">Применение миграций в рабочей среде</span><span class="sxs-lookup"><span data-stu-id="ce64a-164">Applying migrations in production</span></span>

<span data-ttu-id="ce64a-165">Для рабочих приложений **не рекомендуется** вызывать [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ce64a-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="ce64a-166">`Migrate` не следует вызывать из приложения, развернутого в ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="ce64a-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="ce64a-167">Если приложение масштабируется в нескольких экземплярах сервера, трудно гарантировать, что изменения схемы базы данных не будут выполняться с нескольких серверов и не будут конфликтовать с обращениями для чтения или записи.</span><span class="sxs-lookup"><span data-stu-id="ce64a-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="ce64a-168">Миграция базы данных должна выполняться контролируемым способом в рамках развертывания.</span><span class="sxs-lookup"><span data-stu-id="ce64a-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="ce64a-169">Подход к миграции рабочей базы данных включает следующее:</span><span class="sxs-lookup"><span data-stu-id="ce64a-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="ce64a-170">Использование миграций для создания сценариев SQL и их использования в развертывании.</span><span class="sxs-lookup"><span data-stu-id="ce64a-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="ce64a-171">Выполнение `dotnet ef database update` из контролируемой среды.</span><span class="sxs-lookup"><span data-stu-id="ce64a-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ce64a-172">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ce64a-172">Troubleshooting</span></span>

<span data-ttu-id="ce64a-173">Если приложение использует SQL Server LocalDB и выводит следующее исключение:</span><span class="sxs-lookup"><span data-stu-id="ce64a-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="ce64a-174">решением может быть выполнение `dotnet ef database update` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="ce64a-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="ce64a-175">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ce64a-175">Additional resources</span></span>

* <span data-ttu-id="ce64a-176">[Интерфейс командной строки EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="ce64a-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="ce64a-177">Консоль диспетчера пакетов (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="ce64a-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="ce64a-178">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ce64a-178">Next steps</span></span>

<span data-ttu-id="ce64a-179">В следующем руководстве продолжается построение модели данных путем добавления свойств сущностей и новых сущностей.</span><span class="sxs-lookup"><span data-stu-id="ce64a-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ce64a-180">[Предыдущий учебник](xref:data/ef-rp/sort-filter-page)
> [Следующий учебник](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="ce64a-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce64a-181">В этом учебнике используется функция миграций EF Core для управления изменениями модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="ce64a-182">При возникновении проблем, которые вам не удается устранить, скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ce64a-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="ce64a-183">При разработке нового приложения модель данных часто изменяется.</span><span class="sxs-lookup"><span data-stu-id="ce64a-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="ce64a-184">При каждом изменении нарушается синхронизация модели с базой данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="ce64a-185">Вы начали работу с этим учебником, настроив платформу Entity Framework для создания базы данных, если она еще не существует.</span><span class="sxs-lookup"><span data-stu-id="ce64a-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="ce64a-186">Каждый раз при изменении модели данных:</span><span class="sxs-lookup"><span data-stu-id="ce64a-186">Each time the data model changes:</span></span>

* <span data-ttu-id="ce64a-187">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="ce64a-187">The DB is dropped.</span></span>
* <span data-ttu-id="ce64a-188">Платформа EF создает новую базу данных, соответствующую модели.</span><span class="sxs-lookup"><span data-stu-id="ce64a-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="ce64a-189">Приложение заполняет базу тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="ce64a-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="ce64a-190">Этот подход к обеспечению синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="ce64a-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="ce64a-191">Приложение, выполняющееся в рабочей среде, обычно содержит данные.</span><span class="sxs-lookup"><span data-stu-id="ce64a-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="ce64a-192">Приложение не может запускаться с тестовой базой данных каждый раз при внесении изменений (например, при добавлении столбца).</span><span class="sxs-lookup"><span data-stu-id="ce64a-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="ce64a-193">Функция миграций EF Core решает эту проблему, позволяя платформе EF Core обновить схему базы данных вместо создания новой базы.</span><span class="sxs-lookup"><span data-stu-id="ce64a-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="ce64a-194">Вместо удаления и повторного создания базы данных при изменении модели функция миграций обновляет схему, сохраняя существующие данные.</span><span class="sxs-lookup"><span data-stu-id="ce64a-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="ce64a-195">Удаление базы данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-195">Drop the database</span></span>

<span data-ttu-id="ce64a-196">Воспользуйтесь **обозревателем объектов SQL Server** (SSOX) или командой `database drop`:</span><span class="sxs-lookup"><span data-stu-id="ce64a-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce64a-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce64a-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce64a-198">Выполните следующие команды в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="ce64a-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="ce64a-199">Чтобы просмотреть справочную информацию, выполните команду `Get-Help about_EntityFrameworkCore` в PMC.</span><span class="sxs-lookup"><span data-stu-id="ce64a-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce64a-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce64a-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ce64a-201">Откройте командное окно и перейдите в папку проекта.</span><span class="sxs-lookup"><span data-stu-id="ce64a-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ce64a-202">Папка проекта содержит файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="ce64a-203">Введите в командном окне следующее:</span><span class="sxs-lookup"><span data-stu-id="ce64a-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="ce64a-204">Создание первоначальной миграции и обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="ce64a-205">Выполните построение проекта и создайте первую миграцию.</span><span class="sxs-lookup"><span data-stu-id="ce64a-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce64a-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce64a-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce64a-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce64a-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="ce64a-208">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="ce64a-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="ce64a-209">Команда EF Core `migrations add` создала код для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="ce64a-210">Код миграции находится в файле *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="ce64a-211">Метод `Up` класса `InitialCreate` создает таблицы базы данных, соответствующие наборам сущностей модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="ce64a-212">Метод `Down` удаляет их, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ce64a-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="ce64a-213">Функция миграций вызывает метод `Up`, чтобы реализовать изменения модели данных для миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="ce64a-214">При вводе команды для отката обновления функция миграций вызывает метод `Down`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="ce64a-215">Приведенный выше код предназначен для первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="ce64a-216">Этот код был создан при выполнении команды `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="ce64a-217">Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла.</span><span class="sxs-lookup"><span data-stu-id="ce64a-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="ce64a-218">В качестве имени миграции можно использовать любое допустимое имя файла.</span><span class="sxs-lookup"><span data-stu-id="ce64a-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="ce64a-219">Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="ce64a-220">Например, миграция, обеспечивающая добавление таблицы кафедр, может называться "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="ce64a-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="ce64a-221">Если создается первоначальная миграция и база данных существует:</span><span class="sxs-lookup"><span data-stu-id="ce64a-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="ce64a-222">Формируется код создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="ce64a-223">Выполнять код создания базы данных не нужно, поскольку база уже соответствует модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="ce64a-224">В случае выполнения код создания базы данных не вносит никаких изменений, поскольку база уже соответствует модели данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="ce64a-225">При развертывании приложения в новой среде этот код необходимо выполнить для создания новой базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="ce64a-226">Предыдущая база данных была удалена и не существует, поэтому функция миграций создает новую базу данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="ce64a-227">Моментальный снимок модели данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-227">The data model snapshot</span></span>

<span data-ttu-id="ce64a-228">Функция миграций создает *моментальный снимок* текущей схемы базы данных в *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce64a-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="ce64a-229">При добавлении миграции EF определяет, что именно изменилось, сравнивая модель данных с файлом моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="ce64a-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="ce64a-230">Чтобы удалить миграцию, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ce64a-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce64a-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce64a-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce64a-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="ce64a-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce64a-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce64a-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="ce64a-234">Дополнительные сведения см. в разделе [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="ce64a-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="ce64a-235">Команда remove migrations удаляет миграцию и гарантирует корректный сброс моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="ce64a-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="ce64a-236">Удаление EnsureCreated и тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="ce64a-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="ce64a-237">Для ранней разработки использовалась команда `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="ce64a-238">В этом учебнике используются миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="ce64a-239">`EnsureCreated` имеет следующие ограничения:</span><span class="sxs-lookup"><span data-stu-id="ce64a-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="ce64a-240">Пропускает миграции и создает базу данных и схему.</span><span class="sxs-lookup"><span data-stu-id="ce64a-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="ce64a-241">Не создает таблицу миграций.</span><span class="sxs-lookup"><span data-stu-id="ce64a-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="ce64a-242">*Не может* использоваться с миграциями.</span><span class="sxs-lookup"><span data-stu-id="ce64a-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="ce64a-243">Разработана для тестирования или быстрого создания прототипов, когда часто требуется удаление и повторное создание базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="ce64a-244">Удалите `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="ce64a-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="ce64a-245">Запустите приложение и убедитесь, что база заполняется данными.</span><span class="sxs-lookup"><span data-stu-id="ce64a-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="ce64a-246">Проверка базы данных</span><span class="sxs-lookup"><span data-stu-id="ce64a-246">Inspect the database</span></span>

<span data-ttu-id="ce64a-247">Используйте **обозреватель объектов SQL Server** для проверки базы данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="ce64a-248">Обратите внимание на добавление таблицы `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ce64a-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ce64a-249">Таблица `__EFMigrationsHistory` используется для отслеживания миграций, которые были применены к базе данных.</span><span class="sxs-lookup"><span data-stu-id="ce64a-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="ce64a-250">Просмотрев данные в таблице `__EFMigrationsHistory`, вы увидите одну строку для первой миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="ce64a-251">Последний журнал в предыдущем примере выходных данных интерфейса командной строки показывает инструкцию INSERT, создающую эту строку.</span><span class="sxs-lookup"><span data-stu-id="ce64a-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="ce64a-252">Запустите приложение и убедитесь, что все функции работают.</span><span class="sxs-lookup"><span data-stu-id="ce64a-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="ce64a-253">Применение миграций в рабочей среде</span><span class="sxs-lookup"><span data-stu-id="ce64a-253">Applying migrations in production</span></span>

<span data-ttu-id="ce64a-254">Для рабочих приложений **не рекомендуется** вызывать [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ce64a-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="ce64a-255">`Migrate` не следует вызывать из приложения в ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="ce64a-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="ce64a-256">Например, если приложение было развернуто в облаке с горизонтальным масштабированием (выполняется несколько экземпляров приложения).</span><span class="sxs-lookup"><span data-stu-id="ce64a-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="ce64a-257">Миграция базы данных должна выполняться контролируемым способом в рамках развертывания.</span><span class="sxs-lookup"><span data-stu-id="ce64a-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="ce64a-258">Подход к миграции рабочей базы данных включает следующее:</span><span class="sxs-lookup"><span data-stu-id="ce64a-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="ce64a-259">Использование миграций для создания сценариев SQL и их использования в развертывании.</span><span class="sxs-lookup"><span data-stu-id="ce64a-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="ce64a-260">Выполнение `dotnet ef database update` из контролируемой среды.</span><span class="sxs-lookup"><span data-stu-id="ce64a-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="ce64a-261">Платформа EF Core использует таблицу `__MigrationsHistory` для определения необходимости выполнить какие-либо миграции.</span><span class="sxs-lookup"><span data-stu-id="ce64a-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="ce64a-262">Если база данных находится в актуальном состоянии, миграция не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ce64a-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ce64a-263">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ce64a-263">Troubleshooting</span></span>

<span data-ttu-id="ce64a-264">Скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="ce64a-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="ce64a-265">Приложение создает следующее исключение:</span><span class="sxs-lookup"><span data-stu-id="ce64a-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="ce64a-266">Решение: Выполнить `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="ce64a-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="ce64a-267">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ce64a-267">Additional resources</span></span>

* [<span data-ttu-id="ce64a-268">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="ce64a-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="ce64a-269">[Интерфейс командной строки .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="ce64a-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="ce64a-270">Консоль диспетчера пакетов (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="ce64a-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="ce64a-271">[Назад](xref:data/ef-rp/sort-filter-page)
> [Вперед](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="ce64a-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

