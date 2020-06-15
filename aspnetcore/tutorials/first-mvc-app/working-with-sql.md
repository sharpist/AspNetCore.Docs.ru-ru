---
title: Часть 5. Работа с базой данных в приложении MVC ASP.NET Core
author: rick-anderson
description: Часть 5. Добавление модели в приложение MVC ASP.NET Core
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 7fdd147f4de00a593a77977d2b53e5369667fe78
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652518"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="ec241-103">Часть 5. Работа с базой данных в приложении MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec241-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec241-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="ec241-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ec241-105">Объект `MvcMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="ec241-106">Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ec241-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="ec241-108">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ec241-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="ec241-109">Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ec241-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-110">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="ec241-111">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ec241-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="ec241-112">Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ec241-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="ec241-113">Если приложение развертывается на тестовом или рабочем сервере, можно задать строку подключения к рабочему SQL Server с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="ec241-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="ec241-114">Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ec241-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="ec241-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ec241-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ec241-117">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ.</span><span class="sxs-lookup"><span data-stu-id="ec241-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="ec241-118">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="ec241-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ec241-119">По умолчанию база данных LocalDB создает файлы *.mdf* в каталоге *C:/Users/{пользователь}* .</span><span class="sxs-lookup"><span data-stu-id="ec241-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="ec241-120">В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ec241-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Меню "Вид"](working-with-sql/_static/ssox.png)

* <span data-ttu-id="ec241-122">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**.</span><span class="sxs-lookup"><span data-stu-id="ec241-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Контекстное меню, открытое на таблице Movie](working-with-sql/_static/design.png)

  ![Таблица Movie, открытая в конструкторе](working-with-sql/_static/dv.png)

<span data-ttu-id="ec241-125">Обратите внимание на значок с изображением ключа рядом с `ID`.</span><span class="sxs-lookup"><span data-stu-id="ec241-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="ec241-126">По умолчанию EF сделает свойство с именем `ID` первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="ec241-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="ec241-127">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотр данных**.</span><span class="sxs-lookup"><span data-stu-id="ec241-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Контекстное меню, открытое на таблице Movie](working-with-sql/_static/ssox2.png)

  ![Открытая таблица Movie с отображением данных](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-130">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="ec241-131">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="ec241-131">Seed the database</span></span>

<span data-ttu-id="ec241-132">Создайте класс `SeedData` в папке *Models*.</span><span class="sxs-lookup"><span data-stu-id="ec241-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="ec241-133">Замените сгенерированный код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ec241-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="ec241-134">Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.</span><span class="sxs-lookup"><span data-stu-id="ec241-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="ec241-135">Добавление инициализатора заполнения</span><span class="sxs-lookup"><span data-stu-id="ec241-135">Add the seed initializer</span></span>

<span data-ttu-id="ec241-136">Замените содержимое *Program.cs* кодом из этого примера.</span><span class="sxs-lookup"><span data-stu-id="ec241-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="ec241-137">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="ec241-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec241-139">Удалите все записи из базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-139">Delete all the records in the DB.</span></span> <span data-ttu-id="ec241-140">Это можно сделать с помощью ссылок удаления в браузере или из SSOX.</span><span class="sxs-lookup"><span data-stu-id="ec241-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="ec241-141">Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения.</span><span class="sxs-lookup"><span data-stu-id="ec241-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="ec241-142">Для этого следует остановить и перезапустить IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ec241-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="ec241-143">Воспользуйтесь одним из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="ec241-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="ec241-144">Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выход** или **Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="ec241-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Значок IIS Express в области уведомлений](working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="ec241-147">Если среда VS была запущена в режиме без отладки, нажмите клавишу F5 для запуска в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="ec241-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="ec241-148">Если среда VS была запущена в режиме отладки, остановите отладчик и нажмите клавишу F5.</span><span class="sxs-lookup"><span data-stu-id="ec241-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-149">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ec241-150">Удалите все записи в базе данных для запуска метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="ec241-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="ec241-151">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="ec241-152">В приложении будут отображены данные.</span><span class="sxs-lookup"><span data-stu-id="ec241-152">The app shows the seeded data.</span></span>

![Приложение MVC Movie, открытое в Microsoft Edge и отображающие данные о фильме](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="ec241-154">[Назад](adding-model.md)
> [Вперед](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="ec241-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec241-155">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="ec241-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ec241-156">Объект `MvcMovieContext` обрабатывает задачу подключения к базе данных и сопоставления объектов `Movie` с записями базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="ec241-157">Контекст базы данных регистрируется с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection) в методе `ConfigureServices` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ec241-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="ec241-159">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ec241-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="ec241-160">Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ec241-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-161">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="ec241-162">Система [конфигурации](xref:fundamentals/configuration/index) ASP.NET Core считывает `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ec241-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="ec241-163">Для разработки на локальном уровне она получает строку подключения из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ec241-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="ec241-164">При развертывании приложения на тестовом или рабочем сервере вы можете использовать переменную среды или другой способ настройки строки подключения к реальному серверу SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ec241-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="ec241-165">Дополнительные сведения см. в статье [Конфигурация](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ec241-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="ec241-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ec241-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ec241-168">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки программ.</span><span class="sxs-lookup"><span data-stu-id="ec241-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="ec241-169">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="ec241-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ec241-170">По умолчанию база данных LocalDB создает файлы *.mdf* в каталоге *C:/Users/{пользователь}* .</span><span class="sxs-lookup"><span data-stu-id="ec241-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="ec241-171">В меню **Вид** откройте **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ec241-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Меню "Вид"](working-with-sql/_static/ssox.png)

* <span data-ttu-id="ec241-173">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Конструктор представлений**.</span><span class="sxs-lookup"><span data-stu-id="ec241-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Контекстное меню, открытое на таблице Movie](working-with-sql/_static/design.png)

  ![Таблица Movie, открытая в конструкторе](working-with-sql/_static/dv.png)

<span data-ttu-id="ec241-176">Обратите внимание на значок с изображением ключа рядом с `ID`.</span><span class="sxs-lookup"><span data-stu-id="ec241-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="ec241-177">По умолчанию EF сделает свойство с именем `ID` первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="ec241-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="ec241-178">Щелкните правой кнопкой мыши таблицу `Movie` и выберите пункт **Просмотр данных**.</span><span class="sxs-lookup"><span data-stu-id="ec241-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Контекстное меню, открытое на таблице Movie](working-with-sql/_static/ssox2.png)

  ![Открытая таблица Movie с отображением данных](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-181">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="ec241-182">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="ec241-182">Seed the database</span></span>

<span data-ttu-id="ec241-183">Создайте класс `SeedData` в папке *Models*.</span><span class="sxs-lookup"><span data-stu-id="ec241-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="ec241-184">Замените сгенерированный код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ec241-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="ec241-185">Если в базе данных есть фильмы, возвращается инициализатор заполнения и фильмы не добавляются.</span><span class="sxs-lookup"><span data-stu-id="ec241-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="ec241-186">Добавление инициализатора заполнения</span><span class="sxs-lookup"><span data-stu-id="ec241-186">Add the seed initializer</span></span>

<span data-ttu-id="ec241-187">Замените содержимое *Program.cs* кодом из этого примера.</span><span class="sxs-lookup"><span data-stu-id="ec241-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="ec241-188">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="ec241-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec241-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec241-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec241-190">Удалите все записи из базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-190">Delete all the records in the DB.</span></span> <span data-ttu-id="ec241-191">Это можно сделать с помощью ссылок удаления в браузере или из SSOX.</span><span class="sxs-lookup"><span data-stu-id="ec241-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="ec241-192">Необходимо выполнить инициализацию (вызывать методы в классе `Startup`), чтобы запустить метод заполнения.</span><span class="sxs-lookup"><span data-stu-id="ec241-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="ec241-193">Для этого следует остановить и перезапустить IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ec241-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="ec241-194">Воспользуйтесь одним из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="ec241-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="ec241-195">Щелкните правой кнопкой мыши значок IIS Express в области уведомлений и выберите **Выход** или **Остановить сайт**.</span><span class="sxs-lookup"><span data-stu-id="ec241-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Значок IIS Express в области уведомлений](working-with-sql/_static/iisExIcon.png)

    ![Контекстное меню](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="ec241-198">Если среда VS была запущена в режиме без отладки, нажмите клавишу F5 для запуска в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="ec241-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="ec241-199">Если среда VS была запущена в режиме отладки, остановите отладчик и нажмите клавишу F5.</span><span class="sxs-lookup"><span data-stu-id="ec241-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ec241-200">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="ec241-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ec241-201">Удалите все записи в базе данных для запуска метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="ec241-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="ec241-202">Остановите и запустите приложение, чтобы начать заполнение базы данных.</span><span class="sxs-lookup"><span data-stu-id="ec241-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="ec241-203">В приложении будут отображены данные.</span><span class="sxs-lookup"><span data-stu-id="ec241-203">The app shows the seeded data.</span></span>

![Приложение MVC Movie, открытое в Microsoft Edge и отображающие данные о фильме](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="ec241-205">[Назад](adding-model.md)
> [Вперед](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="ec241-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
