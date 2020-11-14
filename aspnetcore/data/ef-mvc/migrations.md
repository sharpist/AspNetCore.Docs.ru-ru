---
title: Руководство по Использование ASP.NET MVC с EF Core. Функции миграций
description: В этом руководстве вы начинаете использовать функцию миграций EF Core для управления изменениями модели данных в приложении ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: 070c18db55956d79560904f53395b5001c7bce6d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054038"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="1a052-103">Руководство по Использование ASP.NET MVC с EF Core. Функции миграций</span><span class="sxs-lookup"><span data-stu-id="1a052-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="1a052-104">В этом руководстве вы начинаете использовать функцию миграций EF Core для управления изменениями модели данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="1a052-105">В последующих руководствах вы добавите дополнительные миграции по мере изменения модели данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="1a052-106">Изучив это руководство, вы:</span><span class="sxs-lookup"><span data-stu-id="1a052-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1a052-107">Дополнительные сведения о миграциях</span><span class="sxs-lookup"><span data-stu-id="1a052-107">Learn about migrations</span></span>
> * <span data-ttu-id="1a052-108">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="1a052-108">Change the connection string</span></span>
> * <span data-ttu-id="1a052-109">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-109">Create an initial migration</span></span>
> * <span data-ttu-id="1a052-110">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="1a052-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="1a052-111">Дополнительные сведения о моментальном снимке модели данных</span><span class="sxs-lookup"><span data-stu-id="1a052-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="1a052-112">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a052-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1a052-113">Prerequisites</span></span>

* [<span data-ttu-id="1a052-114">Сортировка, фильтрация и разбиение на страницы</span><span class="sxs-lookup"><span data-stu-id="1a052-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="1a052-115">Сведения о миграциях</span><span class="sxs-lookup"><span data-stu-id="1a052-115">About migrations</span></span>

<span data-ttu-id="1a052-116">При разработке нового приложения ваша модель данных часто меняется, и при каждом таком изменении она нарушает синхронизацию с базой данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="1a052-117">Вы начали работу с этими руководствами, настроив Entity Framework для создания базы данных, если она еще не существует.</span><span class="sxs-lookup"><span data-stu-id="1a052-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="1a052-118">Затем при каждом изменении модели данных — добавлении, удалении или изменении классов сущностей или изменении класса DbContext — вы можете удалить базу данных, после чего EF создает новую, которая соответствует данной модели, и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1a052-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="1a052-119">Этот способ для обеспечения синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1a052-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="1a052-120">Когда приложение выполняется в рабочей среде, оно обычно хранит данные, которые вы хотите сохранить, и вам нежелательно терять все при каждом изменении, например при добавлении нового столбца.</span><span class="sxs-lookup"><span data-stu-id="1a052-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="1a052-121">Функция миграций EF Core решает эту проблему, позволяя EF обновить схему базы данных вместо создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="1a052-122">Для миграции можно использовать **консоль диспетчера пакетов** (PMC) или CLI.</span><span class="sxs-lookup"><span data-stu-id="1a052-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="1a052-123">Эти руководства демонстрируют использование команд интерфейса командной строки.</span><span class="sxs-lookup"><span data-stu-id="1a052-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="1a052-124">Дополнительные сведения о PMC [приведены в конце этого руководства](#pmc).</span><span class="sxs-lookup"><span data-stu-id="1a052-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="1a052-125">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="1a052-125">Change the connection string</span></span>

<span data-ttu-id="1a052-126">В файле *appsettings.json* измените имя базы данных в строке подключения на ContosoUniversity2 или другое имя, которое вы еще не использовали на компьютере, с которым работаете.</span><span class="sxs-lookup"><span data-stu-id="1a052-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="1a052-127">Это изменение настраивает проект, чтобы первая миграция создала базу данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="1a052-128">Это необязательно для начала работы с миграциями, но позднее вы поймете, почему так стоит сделать.</span><span class="sxs-lookup"><span data-stu-id="1a052-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="1a052-129">Вместо изменения имени базы данных можно удалить ее.</span><span class="sxs-lookup"><span data-stu-id="1a052-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="1a052-130">Воспользуйтесь **обозревателем объектов SQL Server** (SSOX) или командой интерфейса командной строки `database drop`:</span><span class="sxs-lookup"><span data-stu-id="1a052-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="1a052-131">Следующий раздел описывает выполнение команд интерфейса командной строки.</span><span class="sxs-lookup"><span data-stu-id="1a052-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="1a052-132">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-132">Create an initial migration</span></span>

<span data-ttu-id="1a052-133">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="1a052-133">Save your changes and build the project.</span></span> <span data-ttu-id="1a052-134">После этого откройте командное окно и в папку проекта.</span><span class="sxs-lookup"><span data-stu-id="1a052-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="1a052-135">Вот как это можно сделать быстро:</span><span class="sxs-lookup"><span data-stu-id="1a052-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="1a052-136">Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите в контекстном меню пункт **Открыть папку в проводнике**.</span><span class="sxs-lookup"><span data-stu-id="1a052-136">In **Solution Explorer** , right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Элемент меню "Открыть в проводнике"](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="1a052-138">Введите "cmd" в адресной строке и нажмите клавишу ВВОД.</span><span class="sxs-lookup"><span data-stu-id="1a052-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Открытие командного окна](migrations/_static/open-command-window.png)

<span data-ttu-id="1a052-140">Введите в командном окне следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1a052-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="1a052-141">`dotnet tool install --global dotnet-ef` устанавливает `dotnet ef` как [глобальное средство](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="1a052-141">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="1a052-142">Приведенные выше команды выводят результат наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="1a052-142">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="1a052-143">Если отображается сообщение об ошибке " *Доступ к файлу... ContosoUniversity.dll невозможен, так как файл используется другим процессом* ", найдите значок IIS Express в области уведомлений Windows, щелкните его правой кнопкой мыши, а затем выберите **ContosoUniversity > Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="1a052-143">If you see an error message " *cannot access the file ... ContosoUniversity.dll because it is being used by another process.* ", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="1a052-144">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="1a052-144">Examine Up and Down methods</span></span>

<span data-ttu-id="1a052-145">При выполнении команды `migrations add` система EF сформировала код, который создаст базу данных с нуля.</span><span class="sxs-lookup"><span data-stu-id="1a052-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="1a052-146">Этот код находится в файле *\<timestamp>_InitialCreate.cs* внутри папки *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="1a052-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="1a052-147">Метод `Up` класса `InitialCreate` создает таблицы базы данных, которые соответствуют наборам сущностей модели данных, а метод `Down` удаляет их, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="1a052-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="1a052-148">Функция миграций вызывает метод `Up`, чтобы реализовать изменения модели данных для миграции.</span><span class="sxs-lookup"><span data-stu-id="1a052-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="1a052-149">При вводе команды для отката обновления функция миграций вызывает метод `Down`.</span><span class="sxs-lookup"><span data-stu-id="1a052-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="1a052-150">Этот пример кода предназначен для первоначальной миграции, созданной при вводе команды `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="1a052-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="1a052-151">Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла и может быть любым.</span><span class="sxs-lookup"><span data-stu-id="1a052-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="1a052-152">Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции.</span><span class="sxs-lookup"><span data-stu-id="1a052-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="1a052-153">Например, последнюю миграцию можно назвать "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="1a052-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="1a052-154">Если вы создали первоначальную миграцию, когда база данных уже существовала, код для создания базы данных формируется, но выполнять его не требуется, так как база данных уже соответствует модели данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="1a052-155">Однако при развертывании приложения в другой среде, где база данных еще не существует, этот код будет выполняться для создания базы данных, поэтому рекомендуется сначала его протестировать.</span><span class="sxs-lookup"><span data-stu-id="1a052-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="1a052-156">Вот почему ранее вы изменили имя базы данных в строке подключения — это позволяет функции миграций создать базу данных с нуля.</span><span class="sxs-lookup"><span data-stu-id="1a052-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="1a052-157">Моментальный снимок модели данных</span><span class="sxs-lookup"><span data-stu-id="1a052-157">The data model snapshot</span></span>

<span data-ttu-id="1a052-158">Функция миграций создает *моментальный снимок* текущей схемы базы данных в *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="1a052-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="1a052-159">При добавлении миграции EF определяет, что именно изменилось, сравнивая модель данных с файлом моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="1a052-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="1a052-160">Для удаления миграции используйте команду [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="1a052-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="1a052-161">`dotnet ef migrations remove` удаляет миграцию и гарантирует корректный сброс моментального снимка.</span><span class="sxs-lookup"><span data-stu-id="1a052-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="1a052-162">В случае сбоя `dotnet ef migrations remove` используйте `dotnet ef migrations remove -v`, чтобы получить сведения об ошибке.</span><span class="sxs-lookup"><span data-stu-id="1a052-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="1a052-163">Дополнительные сведения об использовании файла моментального снимка см. в разделе [Миграции Core EF в средах групп](/ef/core/managing-schemas/migrations/teams).</span><span class="sxs-lookup"><span data-stu-id="1a052-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="1a052-164">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-164">Apply the migration</span></span>

<span data-ttu-id="1a052-165">Введите следующую команду в командном окне, чтобы создать базу данных и таблицы в ней.</span><span class="sxs-lookup"><span data-stu-id="1a052-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="1a052-166">Выходные данные команды аналогичны команде `migrations add`, за исключением того, что вы видите журналы для команд SQL, настраивающих базу данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="1a052-167">В приведенном ниже примере выходных данных большинство журналов опущено.</span><span class="sxs-lookup"><span data-stu-id="1a052-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="1a052-168">Если вам не нужен такой уровень детализации сообщений журнала, можно изменить уровень ведения журнала в файле *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="1a052-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="1a052-169">Дополнительные сведения см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1a052-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="1a052-170">Используйте **обозреватель объектов SQL Server** для проверки базы данных, как описано в первом руководстве.</span><span class="sxs-lookup"><span data-stu-id="1a052-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="1a052-171">Вы заметите добавление таблицы \_\_EFMigrationsHistory, отслеживающей миграции, которые были применены к базе данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="1a052-172">Просмотрев данные в этой таблице, вы увидите одну строку для первой миграции.</span><span class="sxs-lookup"><span data-stu-id="1a052-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="1a052-173">(Последний журнал в предыдущем примере выходных данных интерфейса командной строки показывает оператор INSERT, создающий эту строку.)</span><span class="sxs-lookup"><span data-stu-id="1a052-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="1a052-174">Запустите приложение, чтобы убедиться, что все работает, как и раньше.</span><span class="sxs-lookup"><span data-stu-id="1a052-174">Run the application to verify that everything still works the same as before.</span></span>

![Страница указателя учащихся](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="1a052-176">Сравнение CLI и PMC</span><span class="sxs-lookup"><span data-stu-id="1a052-176">Compare CLI and PMC</span></span>

<span data-ttu-id="1a052-177">Средства EF для управления миграциями доступны в виде команд интерфейса командной строки .NET Core или командлетов PowerShell в окне **консоли диспетчера пакетов** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1a052-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="1a052-178">Это руководство описывает использование интерфейса командной строки, но вы можете использовать и консоль диспетчера пакетов.</span><span class="sxs-lookup"><span data-stu-id="1a052-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="1a052-179">Команды EF для команд консоли диспетчера пакетов находятся в пакете [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="1a052-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="1a052-180">Этот пакет входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), поэтому если приложение уже содержит ссылку на пакет `Microsoft.AspNetCore.App`, добавлять ссылку на пакет не нужно.</span><span class="sxs-lookup"><span data-stu-id="1a052-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="1a052-181">**Внимание!** Это не тот же пакет, который вы устанавливаете для CLI, изменив файл *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="1a052-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="1a052-182">Его имя заканчивается на `Tools`, а имя пакета интерфейса командной строки — на `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="1a052-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="1a052-183">Дополнительные сведения о командах интерфейса командной строки см. в разделе [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="1a052-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="1a052-184">Дополнительные сведения о командах консоли диспетчера пакетов см. в разделе [Консоль диспетчера пакетов (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="1a052-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="1a052-185">Получение кода</span><span class="sxs-lookup"><span data-stu-id="1a052-185">Get the code</span></span>

[<span data-ttu-id="1a052-186">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="1a052-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="1a052-187">Следующий шаг</span><span class="sxs-lookup"><span data-stu-id="1a052-187">Next step</span></span>

<span data-ttu-id="1a052-188">Изучив это руководство, вы:</span><span class="sxs-lookup"><span data-stu-id="1a052-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1a052-189">Дополнительные сведения о миграциях</span><span class="sxs-lookup"><span data-stu-id="1a052-189">Learned about migrations</span></span>
> * <span data-ttu-id="1a052-190">Дополнительные сведения о пакетах миграции NuGet</span><span class="sxs-lookup"><span data-stu-id="1a052-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="1a052-191">Изменение строки подключения</span><span class="sxs-lookup"><span data-stu-id="1a052-191">Changed the connection string</span></span>
> * <span data-ttu-id="1a052-192">Создание первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-192">Created an initial migration</span></span>
> * <span data-ttu-id="1a052-193">Обзор методов Up и Down</span><span class="sxs-lookup"><span data-stu-id="1a052-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="1a052-194">Дополнительные сведения о моментальном снимке модели данных</span><span class="sxs-lookup"><span data-stu-id="1a052-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="1a052-195">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="1a052-195">Applied the migration</span></span>

<span data-ttu-id="1a052-196">В следующем учебнике описаны более сложные вопросы, связанные с развертыванием модели данных.</span><span class="sxs-lookup"><span data-stu-id="1a052-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="1a052-197">Попутно вы создадите и примените дополнительные миграции.</span><span class="sxs-lookup"><span data-stu-id="1a052-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1a052-198">Создание и применение дополнительных миграций</span><span class="sxs-lookup"><span data-stu-id="1a052-198">Create and apply additional migrations</span></span>](complex-data-model.md)
