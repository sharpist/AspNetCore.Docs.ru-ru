---
title: Часть 4. Добавление модели в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 4 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688453"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="13327-103">Часть 4. Добавление модели в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13327-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="13327-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="13327-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="13327-105">В этом разделе мы добавим классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="13327-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="13327-106">Эти классы будут представлять уровень **м** одели в приложении **M** VC.</span><span class="sxs-lookup"><span data-stu-id="13327-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="13327-107">Эти классы используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="13327-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="13327-108">EF Core —это платформа объектно реляционного сопоставления (ORM), которая позволяет упростить необходимый код доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="13327-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="13327-109">Создаваемые вами классы моделей называются классами POCO (от **P** lain **O** ld **C** LR **O** — старые добрые объекты CLR), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="13327-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="13327-110">Эти классы просто определяют свойства данных, которые будут храниться в базе данных.</span><span class="sxs-lookup"><span data-stu-id="13327-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="13327-111">Работая с этим учебником, вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="13327-112">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="13327-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-114">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="13327-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="13327-115">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="13327-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="13327-117">Добавьте файл *Movie.cs* в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="13327-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="13327-119">Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="13327-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="13327-120">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="13327-121">Обновите файл *Movie.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="13327-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="13327-122">Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="13327-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="13327-123">Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="13327-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="13327-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="13327-124">With this attribute:</span></span>

* <span data-ttu-id="13327-125">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="13327-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="13327-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="13327-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="13327-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="13327-128">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="13327-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-130">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="13327-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="13327-132">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="13327-133">Приведенная выше команда добавляет поставщик EF Core для SQL Server.</span><span class="sxs-lookup"><span data-stu-id="13327-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="13327-134">Пакет поставщика устанавливает пакет EF Core в качестве зависимости.</span><span class="sxs-lookup"><span data-stu-id="13327-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="13327-135">Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="13327-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-137">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="13327-138">В меню **Проект** выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="13327-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="13327-139">В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**.</span><span class="sxs-lookup"><span data-stu-id="13327-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="13327-140">Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="13327-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="13327-142">Откроется диалоговое окно **Выбор проектов**, в котором будет выбран проект `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="13327-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="13327-143">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="13327-143">Press the **Ok** button.</span></span>

<span data-ttu-id="13327-144">Появится диалоговое окно **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="13327-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="13327-145">Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.</span><span class="sxs-lookup"><span data-stu-id="13327-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="13327-146">Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="13327-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="13327-147">Выполните следующую команду CLI .NET:</span><span class="sxs-lookup"><span data-stu-id="13327-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="13327-148">Предыдущая команда добавляет [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="13327-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="13327-149">Создание класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-149">Create a database context class</span></span>

<span data-ttu-id="13327-150">Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="13327-151">Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.</span><span class="sxs-lookup"><span data-stu-id="13327-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="13327-152">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="13327-152">Create a *Data* folder.</span></span>

<span data-ttu-id="13327-153">Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="13327-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="13327-154">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="13327-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="13327-155">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="13327-156">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="13327-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="13327-157">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-157">Register the database context</span></span>

<span data-ttu-id="13327-158">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="13327-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="13327-159">Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="13327-160">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="13327-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="13327-161">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="13327-162">В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="13327-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="13327-163">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="13327-164">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="13327-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-166">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="13327-167">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="13327-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="13327-168">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="13327-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="13327-169">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-169">Add a database connection string</span></span>

<span data-ttu-id="13327-170">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="13327-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-172">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="13327-173">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="13327-174">Формирования шаблона страниц фильмов</span><span class="sxs-lookup"><span data-stu-id="13327-174">Scaffold movie pages</span></span>

<span data-ttu-id="13327-175">Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="13327-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-177">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="13327-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="13327-179">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="13327-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="13327-181">Выполните необходимые действия в диалоговом окне **Добавление контроллера**:</span><span class="sxs-lookup"><span data-stu-id="13327-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="13327-182">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="13327-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="13327-183">**Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="13327-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Добавление контекста данных](adding-model/_static/dc5.png)

* <span data-ttu-id="13327-185">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="13327-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="13327-186">**Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)</span><span class="sxs-lookup"><span data-stu-id="13327-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="13327-187">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="13327-187">Select **Add**</span></span>

<span data-ttu-id="13327-188">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="13327-188">Visual Studio creates:</span></span>

* <span data-ttu-id="13327-189">контроллер фильмов (*Controllers/MoviesController.cs*);</span><span class="sxs-lookup"><span data-stu-id="13327-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="13327-190">файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="13327-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="13327-191">Автоматическое создание этих файлов называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="13327-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="13327-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="13327-193">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="13327-194">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="13327-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="13327-195">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-196">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="13327-197">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="13327-198">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="13327-199">Сформированные страницы пока использовать нельзя, так как база данных не существует.</span><span class="sxs-lookup"><span data-stu-id="13327-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="13327-200">Если запустить приложение и щелкнуть ссылку **Movie App**, появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.</span><span class="sxs-lookup"><span data-stu-id="13327-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="13327-201">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="13327-201">Initial migration</span></span>

<span data-ttu-id="13327-202">Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="13327-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="13327-203">Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.</span><span class="sxs-lookup"><span data-stu-id="13327-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-205">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="13327-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="13327-206">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="13327-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="13327-207">`Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="13327-208">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="13327-209">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="13327-210">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="13327-211">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="13327-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="13327-212">`Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="13327-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="13327-213">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="13327-214">Команда обновления базы данных выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="13327-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="13327-215">"Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="13327-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="13327-216">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="13327-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="13327-217">С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="13327-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="13327-218">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-219">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="13327-220">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="13327-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="13327-221">`ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="13327-222">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="13327-223">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="13327-224">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="13327-225">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="13327-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="13327-226">`ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="13327-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="13327-227">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="13327-228">Класс InitialCreate</span><span class="sxs-lookup"><span data-stu-id="13327-228">The InitialCreate class</span></span>

<span data-ttu-id="13327-229">Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="13327-230">Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="13327-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="13327-231">Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.</span><span class="sxs-lookup"><span data-stu-id="13327-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="13327-232">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="13327-232">Test the app</span></span>

* <span data-ttu-id="13327-233">Запустите приложение и щелкните ссылку **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="13327-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="13327-234">Если возникнет исключение наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="13327-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-236">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="13327-237">возможно, вы пропустили [шаг миграции](#migration).</span><span class="sxs-lookup"><span data-stu-id="13327-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="13327-238">Протестируйте страницу **создания**.</span><span class="sxs-lookup"><span data-stu-id="13327-238">Test the **Create** page.</span></span> <span data-ttu-id="13327-239">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="13327-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="13327-240">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="13327-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="13327-241">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="13327-242">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="13327-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="13327-243">Протестируйте страницы **редактирования**, **сведений** и **удаления**.</span><span class="sxs-lookup"><span data-stu-id="13327-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="13327-244">Внедрение зависимостей в контроллере</span><span class="sxs-lookup"><span data-stu-id="13327-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-246">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="13327-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="13327-247">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="13327-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="13327-248">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="13327-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-249">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="13327-250">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="13327-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="13327-251">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="13327-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="13327-252">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="13327-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="13327-253">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="13327-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="13327-254">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="13327-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="13327-255">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="13327-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="13327-256">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="13327-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="13327-257">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="13327-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="13327-258">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="13327-259">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="13327-260">Такой подход обеспечивает проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="13327-261">В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="13327-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="13327-262">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="13327-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="13327-263">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="13327-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="13327-264">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="13327-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="13327-265">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="13327-266">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="13327-267">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="13327-268">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="13327-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="13327-269">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="13327-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="13327-270">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="13327-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="13327-271">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="13327-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="13327-272">Изучите содержимое файла *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="13327-273">Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="13327-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="13327-274">При создании контроллера movie был включен следующий оператор `@model`:</span><span class="sxs-lookup"><span data-stu-id="13327-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="13327-275">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="13327-276">Объект `Model` является строго типизированным.</span><span class="sxs-lookup"><span data-stu-id="13327-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="13327-277">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="13327-278">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="13327-279">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="13327-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="13327-280">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="13327-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="13327-281">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="13327-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="13327-282">При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="13327-283">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="13327-284">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="13327-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="13327-285">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="13327-286">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13327-287">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="13327-287">Additional resources</span></span>

* [<span data-ttu-id="13327-288">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="13327-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="13327-289">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="13327-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="13327-290">[Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="13327-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="13327-291">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="13327-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-293">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="13327-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="13327-294">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="13327-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="13327-296">Добавьте файл *Movie.cs* в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="13327-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-297">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="13327-298">Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="13327-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="13327-299">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="13327-300">Обновите файл *Movie.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="13327-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="13327-301">Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="13327-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="13327-302">Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="13327-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="13327-303">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="13327-303">With this attribute:</span></span>

* <span data-ttu-id="13327-304">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="13327-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="13327-305">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="13327-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="13327-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="13327-307">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="13327-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-309">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="13327-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="13327-311">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="13327-312">Приведенная выше команда добавляет поставщик EF Core для SQL Server.</span><span class="sxs-lookup"><span data-stu-id="13327-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="13327-313">Пакет поставщика устанавливает пакет EF Core в качестве зависимости.</span><span class="sxs-lookup"><span data-stu-id="13327-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="13327-314">Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="13327-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-316">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="13327-317">В меню **Проект** выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="13327-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="13327-318">В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**.</span><span class="sxs-lookup"><span data-stu-id="13327-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="13327-319">Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="13327-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="13327-321">Откроется диалоговое окно **Выбор проектов**, в котором будет выбран проект `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="13327-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="13327-322">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="13327-322">Press the **Ok** button.</span></span>

<span data-ttu-id="13327-323">Появится диалоговое окно **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="13327-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="13327-324">Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.</span><span class="sxs-lookup"><span data-stu-id="13327-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="13327-325">Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="13327-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="13327-326">Создание класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-326">Create a database context class</span></span>

<span data-ttu-id="13327-327">Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="13327-328">Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.</span><span class="sxs-lookup"><span data-stu-id="13327-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="13327-329">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="13327-329">Create a *Data* folder.</span></span>

<span data-ttu-id="13327-330">Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="13327-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="13327-331">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="13327-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="13327-332">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="13327-333">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="13327-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="13327-334">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-334">Register the database context</span></span>

<span data-ttu-id="13327-335">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="13327-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="13327-336">Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="13327-337">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="13327-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="13327-338">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="13327-339">В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="13327-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="13327-340">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="13327-341">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="13327-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-343">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="13327-344">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="13327-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="13327-345">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="13327-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="13327-346">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="13327-346">Add a database connection string</span></span>

<span data-ttu-id="13327-347">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="13327-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-349">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="13327-350">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="13327-351">Формирования шаблона страниц фильмов</span><span class="sxs-lookup"><span data-stu-id="13327-351">Scaffold movie pages</span></span>

<span data-ttu-id="13327-352">Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="13327-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-354">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="13327-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="13327-356">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="13327-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="13327-358">Выполните необходимые действия в диалоговом окне **Добавление контроллера**:</span><span class="sxs-lookup"><span data-stu-id="13327-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="13327-359">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="13327-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="13327-360">**Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="13327-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Добавление контекста данных](adding-model/_static/dc3.png)

* <span data-ttu-id="13327-362">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="13327-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="13327-363">**Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)</span><span class="sxs-lookup"><span data-stu-id="13327-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="13327-364">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="13327-364">Select **Add**</span></span>

<span data-ttu-id="13327-365">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="13327-365">Visual Studio creates:</span></span>

* <span data-ttu-id="13327-366">контроллер фильмов (*Controllers/MoviesController.cs*);</span><span class="sxs-lookup"><span data-stu-id="13327-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="13327-367">файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="13327-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="13327-368">Автоматическое создание этих файлов называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="13327-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="13327-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="13327-370">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="13327-371">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="13327-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="13327-372">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-373">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="13327-374">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="13327-375">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="13327-376">Сформированные страницы пока использовать нельзя, так как база данных не существует.</span><span class="sxs-lookup"><span data-stu-id="13327-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="13327-377">Если запустить приложение и щелкнуть ссылку **Movie App**, появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.</span><span class="sxs-lookup"><span data-stu-id="13327-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="13327-378">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="13327-378">Initial migration</span></span>

<span data-ttu-id="13327-379">Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="13327-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="13327-380">Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.</span><span class="sxs-lookup"><span data-stu-id="13327-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-382">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="13327-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="13327-383">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="13327-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="13327-384">`Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="13327-385">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="13327-386">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="13327-387">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="13327-388">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="13327-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="13327-389">`Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="13327-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="13327-390">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="13327-391">Команда обновления базы данных выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="13327-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="13327-392">"Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="13327-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="13327-393">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="13327-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="13327-394">С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="13327-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="13327-395">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-396">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="13327-397">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="13327-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="13327-398">`ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="13327-399">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="13327-400">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="13327-401">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="13327-402">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="13327-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="13327-403">`ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="13327-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="13327-404">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="13327-405">Класс InitialCreate</span><span class="sxs-lookup"><span data-stu-id="13327-405">The InitialCreate class</span></span>

<span data-ttu-id="13327-406">Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="13327-407">Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="13327-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="13327-408">Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.</span><span class="sxs-lookup"><span data-stu-id="13327-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="13327-409">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="13327-409">Test the app</span></span>

* <span data-ttu-id="13327-410">Запустите приложение и щелкните ссылку **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="13327-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="13327-411">Если возникнет исключение наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="13327-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-413">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="13327-414">возможно, вы пропустили [шаг миграции](#migration).</span><span class="sxs-lookup"><span data-stu-id="13327-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="13327-415">Протестируйте страницу **создания**.</span><span class="sxs-lookup"><span data-stu-id="13327-415">Test the **Create** page.</span></span> <span data-ttu-id="13327-416">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="13327-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="13327-417">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="13327-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="13327-418">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="13327-419">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="13327-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="13327-420">Протестируйте страницы **редактирования**, **сведений** и **удаления**.</span><span class="sxs-lookup"><span data-stu-id="13327-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="13327-421">Внедрение зависимостей в контроллере</span><span class="sxs-lookup"><span data-stu-id="13327-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-423">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="13327-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="13327-424">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="13327-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="13327-425">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="13327-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-426">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="13327-427">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="13327-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="13327-428">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="13327-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="13327-429">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="13327-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="13327-430">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="13327-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="13327-431">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="13327-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="13327-432">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="13327-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="13327-433">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="13327-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="13327-434">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="13327-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="13327-435">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="13327-436">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="13327-437">Такой подход обеспечивает проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="13327-438">В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="13327-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="13327-439">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="13327-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="13327-440">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="13327-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="13327-441">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="13327-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="13327-442">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="13327-443">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="13327-444">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="13327-445">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="13327-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="13327-446">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="13327-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="13327-447">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="13327-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="13327-448">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="13327-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="13327-449">Изучите содержимое файла *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="13327-450">Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="13327-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="13327-451">При создании контроллера movie был включен следующий оператор `@model`:</span><span class="sxs-lookup"><span data-stu-id="13327-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="13327-452">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="13327-453">Объект `Model` является строго типизированным.</span><span class="sxs-lookup"><span data-stu-id="13327-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="13327-454">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="13327-455">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="13327-456">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="13327-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="13327-457">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="13327-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="13327-458">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="13327-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="13327-459">При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="13327-460">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="13327-461">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="13327-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="13327-462">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="13327-463">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13327-464">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="13327-464">Additional resources</span></span>

* [<span data-ttu-id="13327-465">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="13327-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="13327-466">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="13327-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="13327-467">[Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="13327-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="13327-468">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="13327-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-470">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="13327-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="13327-471">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="13327-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-472">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="13327-473">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="13327-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="13327-474">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="13327-474">Scaffold the movie model</span></span>

<span data-ttu-id="13327-475">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="13327-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="13327-476">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="13327-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-478">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="13327-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="13327-480">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="13327-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="13327-482">Выполните необходимые действия в диалоговом окне **Добавление контроллера**:</span><span class="sxs-lookup"><span data-stu-id="13327-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="13327-483">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="13327-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="13327-484">**Класс контекста данных:** щелкните значок **+** и добавьте класс **MvcMovie.Models.MvcMovieContext** по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="13327-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Добавление контекста данных](adding-model/_static/dc.png)

* <span data-ttu-id="13327-486">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="13327-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="13327-487">**Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)</span><span class="sxs-lookup"><span data-stu-id="13327-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="13327-488">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="13327-488">Select **Add**</span></span>

![Диалоговое окно "Добавление контроллера"](adding-model/_static/add_controller2.png)

<span data-ttu-id="13327-490">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="13327-490">Visual Studio creates:</span></span>

* <span data-ttu-id="13327-491">[класс контекста базы данных](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*);</span><span class="sxs-lookup"><span data-stu-id="13327-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="13327-492">контроллер фильмов (*Controllers/MoviesController.cs*);</span><span class="sxs-lookup"><span data-stu-id="13327-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="13327-493">файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="13327-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="13327-494">Автоматическое создание контекста базы данных и методов и представлений действий [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="13327-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="13327-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="13327-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="13327-496">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="13327-497">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="13327-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="13327-498">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="13327-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="13327-499">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="13327-500">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="13327-501">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="13327-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="13327-502">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="13327-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="13327-503">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="13327-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="13327-504">Если запустить приложение и щелкнуть ссылку **Mvc Movie**, возникает ошибка наподобие следующей:</span><span class="sxs-lookup"><span data-stu-id="13327-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-506">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="13327-507">Вам необходимо создать базу данных. Для этого вы используете функцию [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="13327-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="13327-508">С помощью миграций можно создать базу данных, соответствующую модели данных, и обновлять схему базы данных при изменении модели.</span><span class="sxs-lookup"><span data-stu-id="13327-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="13327-509">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="13327-509">Initial migration</span></span>

<span data-ttu-id="13327-510">В этом разделе выполняются следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="13327-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="13327-511">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-511">Add an initial migration.</span></span>
* <span data-ttu-id="13327-512">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="13327-514">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="13327-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="13327-516">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="13327-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="13327-517">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="13327-518">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="13327-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="13327-519">Аргумент `Initial` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="13327-520">Можно использовать любое имя, но по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="13327-521">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="13327-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="13327-522">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="13327-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-523">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="13327-524">Команда `ef migrations add InitialCreate` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="13327-525">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="13327-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="13327-526">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="13327-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="13327-527">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="13327-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="13327-528">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="13327-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="13327-529">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="13327-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="13327-530">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="13327-531">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="13327-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="13327-532">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="13327-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="13327-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13327-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13327-534">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="13327-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="13327-535">Рассмотрим следующий метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="13327-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="13327-536">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="13327-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="13327-537">`MvcMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="13327-538">Контекст данных (`MvcMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="13327-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="13327-539">Контекст данных указывает сущности, которые включаются в модель данных:</span><span class="sxs-lookup"><span data-stu-id="13327-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="13327-540">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="13327-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="13327-541">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="13327-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="13327-542">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="13327-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="13327-543">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="13327-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="13327-544">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="13327-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="13327-545">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="13327-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="13327-546">Вы создаете контекст базы данных и регистрируете его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="13327-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="13327-547">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="13327-547">Test the app</span></span>

* <span data-ttu-id="13327-548">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="13327-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="13327-549">Если вы получите исключение базы данных, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="13327-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="13327-550">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="13327-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="13327-551">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="13327-551">Test the **Create** link.</span></span> <span data-ttu-id="13327-552">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="13327-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="13327-553">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="13327-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="13327-554">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="13327-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="13327-555">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="13327-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="13327-556">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="13327-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="13327-557">Проверьте класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="13327-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="13327-558">Выделенный выше код демонстрирует добавление контекста базы данных фильмов в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="13327-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="13327-559">`services.AddDbContext<MvcMovieContext>(options =>` задает используемую базу данных и строку подключения.</span><span class="sxs-lookup"><span data-stu-id="13327-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="13327-560">`=>` — это [лямбда-оператор](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="13327-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="13327-561">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="13327-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="13327-562">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="13327-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="13327-563">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="13327-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="13327-564">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="13327-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="13327-565">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="13327-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="13327-566">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="13327-567">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="13327-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="13327-568">Такой подход обеспечивает более эффективную проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="13327-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="13327-569">При создании методов и представлений в этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классами `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="13327-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="13327-570">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="13327-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="13327-571">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="13327-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="13327-572">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="13327-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="13327-573">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="13327-574">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="13327-575">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="13327-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="13327-576">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="13327-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="13327-577">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="13327-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="13327-578">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="13327-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="13327-579">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="13327-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="13327-580">Изучите содержимое файла *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="13327-581">Указав оператор `@model` в начале файла представления, вы можете задать тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="13327-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="13327-582">При создании контроллера movie следующий оператор `@model` был автоматически добавлен в начало файла *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="13327-583">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="13327-584">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="13327-585">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="13327-586">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="13327-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="13327-587">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="13327-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="13327-588">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="13327-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="13327-589">При создании контроллера movies механизм формирования шаблонов автоматически включает следующий оператор `@model` в начало файла *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="13327-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="13327-590">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="13327-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="13327-591">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="13327-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="13327-592">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="13327-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="13327-593">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции:</span><span class="sxs-lookup"><span data-stu-id="13327-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13327-594">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="13327-594">Additional resources</span></span>

* [<span data-ttu-id="13327-595">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="13327-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="13327-596">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="13327-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="13327-597">[Назад: добавление представления](adding-view.md)
> [Далее: работа с базой данных](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="13327-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
