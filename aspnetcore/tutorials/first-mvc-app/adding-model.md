---
title: Часть 4. Добавление модели в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 4 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 0974abfe632fd897c08435b82a7c695534cc3139
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793598"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="19b7c-103">Часть 4. Добавление модели в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19b7c-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="19b7c-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="19b7c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="19b7c-105">В этом разделе мы добавим классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="19b7c-106">Эти классы будут представлять уровень **м**одели в приложении **M**VC.</span><span class="sxs-lookup"><span data-stu-id="19b7c-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="19b7c-107">Эти классы используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="19b7c-108">EF Core —это платформа объектно реляционного сопоставления (ORM), которая позволяет упростить необходимый код доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="19b7c-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="19b7c-109">Создаваемые вами классы моделей называются классами POCO (от **P**lain **O**ld **C**LR **O** — старые добрые объекты CLR), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="19b7c-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="19b7c-110">Эти классы просто определяют свойства данных, которые будут храниться в базе данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="19b7c-111">Работая с этим учебником, вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="19b7c-112">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="19b7c-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-114">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="19b7c-115">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19b7c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19b7c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="19b7c-117">Добавьте файл *Movie.cs* в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="19b7c-119">Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="19b7c-120">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="19b7c-121">Обновите файл *Movie.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="19b7c-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="19b7c-122">Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="19b7c-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="19b7c-123">Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="19b7c-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="19b7c-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="19b7c-124">With this attribute:</span></span>

* <span data-ttu-id="19b7c-125">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="19b7c-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="19b7c-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="19b7c-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="19b7c-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="19b7c-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="19b7c-128">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="19b7c-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-130">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="19b7c-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="19b7c-132">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="19b7c-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="19b7c-133">Приведенная выше команда добавляет поставщик EF Core для SQL Server.</span><span class="sxs-lookup"><span data-stu-id="19b7c-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="19b7c-134">Пакет поставщика устанавливает пакет EF Core в качестве зависимости.</span><span class="sxs-lookup"><span data-stu-id="19b7c-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="19b7c-135">Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="19b7c-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19b7c-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19b7c-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-137">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="19b7c-138">В меню **Проект** выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="19b7c-139">В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="19b7c-140">Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="19b7c-142">Откроется диалоговое окно **Выбор проектов**, в котором будет выбран проект `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="19b7c-143">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-143">Press the **Ok** button.</span></span>

<span data-ttu-id="19b7c-144">Появится диалоговое окно **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="19b7c-145">Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="19b7c-146">Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="19b7c-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="19b7c-147">Создание класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="19b7c-147">Create a database context class</span></span>

<span data-ttu-id="19b7c-148">Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="19b7c-149">Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="19b7c-150">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-150">Create a *Data* folder.</span></span>

<span data-ttu-id="19b7c-151">Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="19b7c-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="19b7c-152">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="19b7c-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="19b7c-153">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="19b7c-154">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="19b7c-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="19b7c-155">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="19b7c-155">Register the database context</span></span>

<span data-ttu-id="19b7c-156">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="19b7c-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="19b7c-157">Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="19b7c-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="19b7c-158">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="19b7c-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="19b7c-159">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="19b7c-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="19b7c-160">В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="19b7c-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="19b7c-161">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="19b7c-162">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-164">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="19b7c-165">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="19b7c-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="19b7c-166">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="19b7c-167">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="19b7c-167">Add a database connection string</span></span>

<span data-ttu-id="19b7c-168">Добавьте строку подключения в файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-170">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="19b7c-171">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="19b7c-172">Формирования шаблона страниц фильмов</span><span class="sxs-lookup"><span data-stu-id="19b7c-172">Scaffold movie pages</span></span>

<span data-ttu-id="19b7c-173">Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="19b7c-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-175">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="19b7c-177">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="19b7c-179">Выполните необходимые действия в диалоговом окне **Добавление контроллера**:</span><span class="sxs-lookup"><span data-stu-id="19b7c-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="19b7c-180">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="19b7c-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="19b7c-181">**Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="19b7c-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Добавление контекста данных](adding-model/_static/dc3.png)

* <span data-ttu-id="19b7c-183">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="19b7c-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="19b7c-184">**Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)</span><span class="sxs-lookup"><span data-stu-id="19b7c-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="19b7c-185">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="19b7c-185">Select **Add**</span></span>

<span data-ttu-id="19b7c-186">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="19b7c-186">Visual Studio creates:</span></span>

* <span data-ttu-id="19b7c-187">контроллер фильмов (*Controllers/MoviesController.cs*);</span><span class="sxs-lookup"><span data-stu-id="19b7c-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="19b7c-188">файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="19b7c-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="19b7c-189">Автоматическое создание этих файлов называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="19b7c-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19b7c-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="19b7c-191">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="19b7c-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="19b7c-192">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="19b7c-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="19b7c-193">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="19b7c-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-194">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19b7c-195">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="19b7c-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="19b7c-196">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="19b7c-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="19b7c-197">Сформированные страницы пока использовать нельзя, так как база данных не существует.</span><span class="sxs-lookup"><span data-stu-id="19b7c-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="19b7c-198">Если запустить приложение и щелкнуть ссылку **Movie App**, появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="19b7c-199">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="19b7c-199">Initial migration</span></span>

<span data-ttu-id="19b7c-200">Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="19b7c-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="19b7c-201">Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-203">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="19b7c-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="19b7c-204">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="19b7c-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="19b7c-205">`Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="19b7c-206">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="19b7c-207">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="19b7c-208">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="19b7c-209">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="19b7c-210">`Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="19b7c-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="19b7c-211">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="19b7c-212">Команда обновления базы данных выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="19b7c-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="19b7c-213">"Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="19b7c-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="19b7c-214">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="19b7c-215">С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="19b7c-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="19b7c-216">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="19b7c-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-217">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="19b7c-218">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="19b7c-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="19b7c-219">`ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="19b7c-220">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="19b7c-221">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="19b7c-222">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="19b7c-223">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="19b7c-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="19b7c-224">`ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="19b7c-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="19b7c-225">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="19b7c-226">Класс InitialCreate</span><span class="sxs-lookup"><span data-stu-id="19b7c-226">The InitialCreate class</span></span>

<span data-ttu-id="19b7c-227">Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="19b7c-228">Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="19b7c-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="19b7c-229">Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="19b7c-230">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="19b7c-230">Test the app</span></span>

* <span data-ttu-id="19b7c-231">Запустите приложение и щелкните ссылку **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="19b7c-232">Если возникнет исключение наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="19b7c-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-234">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="19b7c-235">возможно, вы пропустили [шаг миграции](#migration).</span><span class="sxs-lookup"><span data-stu-id="19b7c-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="19b7c-236">Протестируйте страницу **создания**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-236">Test the **Create** page.</span></span> <span data-ttu-id="19b7c-237">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="19b7c-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="19b7c-238">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="19b7c-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="19b7c-239">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="19b7c-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="19b7c-240">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="19b7c-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="19b7c-241">Протестируйте страницы **редактирования**, **сведений** и **удаления**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="19b7c-242">Внедрение зависимостей в контроллере</span><span class="sxs-lookup"><span data-stu-id="19b7c-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-244">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="19b7c-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="19b7c-245">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="19b7c-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="19b7c-246">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="19b7c-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-247">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="19b7c-248">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="19b7c-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="19b7c-249">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="19b7c-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="19b7c-250">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="19b7c-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="19b7c-251">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="19b7c-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="19b7c-252">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="19b7c-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="19b7c-253">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="19b7c-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="19b7c-254">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="19b7c-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="19b7c-255">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="19b7c-256">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="19b7c-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="19b7c-257">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="19b7c-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="19b7c-258">Такой подход обеспечивает проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="19b7c-259">В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="19b7c-260">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="19b7c-261">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="19b7c-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="19b7c-262">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="19b7c-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="19b7c-263">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="19b7c-264">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="19b7c-265">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="19b7c-266">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="19b7c-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="19b7c-267">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="19b7c-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="19b7c-268">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="19b7c-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="19b7c-269">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="19b7c-270">Изучите содержимое файла *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="19b7c-271">Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="19b7c-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="19b7c-272">При создании контроллера movie был включен следующий оператор `@model`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="19b7c-273">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление.</span><span class="sxs-lookup"><span data-stu-id="19b7c-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="19b7c-274">Объект `Model` является строго типизированным.</span><span class="sxs-lookup"><span data-stu-id="19b7c-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="19b7c-275">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="19b7c-276">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="19b7c-277">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="19b7c-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="19b7c-278">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="19b7c-279">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="19b7c-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="19b7c-280">При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="19b7c-281">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="19b7c-282">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="19b7c-283">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="19b7c-284">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19b7c-285">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="19b7c-285">Additional resources</span></span>

* [<span data-ttu-id="19b7c-286">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="19b7c-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="19b7c-287">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="19b7c-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="19b7c-288">[Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="19b7c-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="19b7c-289">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="19b7c-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-291">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="19b7c-292">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-293">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="19b7c-294">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="19b7c-295">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="19b7c-295">Scaffold the movie model</span></span>

<span data-ttu-id="19b7c-296">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="19b7c-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="19b7c-297">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="19b7c-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-299">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="19b7c-301">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="19b7c-303">Выполните необходимые действия в диалоговом окне **Добавление контроллера**:</span><span class="sxs-lookup"><span data-stu-id="19b7c-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="19b7c-304">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="19b7c-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="19b7c-305">**Класс контекста данных:** щелкните значок **+** и добавьте класс **MvcMovie.Models.MvcMovieContext** по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Добавление контекста данных](adding-model/_static/dc.png)

* <span data-ttu-id="19b7c-307">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="19b7c-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="19b7c-308">**Имя контроллера:** оставьте имя по умолчанию (*MoviesController*)</span><span class="sxs-lookup"><span data-stu-id="19b7c-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="19b7c-309">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="19b7c-309">Select **Add**</span></span>

![Диалоговое окно "Добавление контроллера"](adding-model/_static/add_controller2.png)

<span data-ttu-id="19b7c-311">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="19b7c-311">Visual Studio creates:</span></span>

* <span data-ttu-id="19b7c-312">[класс контекста базы данных](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*);</span><span class="sxs-lookup"><span data-stu-id="19b7c-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="19b7c-313">контроллер фильмов (*Controllers/MoviesController.cs*);</span><span class="sxs-lookup"><span data-stu-id="19b7c-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="19b7c-314">файлы представления Razor для страниц Create, Delete, Details, Edit и Index (*Views/Movies/\*.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="19b7c-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="19b7c-315">Автоматическое создание контекста базы данных и методов и представлений действий [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19b7c-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19b7c-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="19b7c-317">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="19b7c-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="19b7c-318">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="19b7c-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="19b7c-319">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="19b7c-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="19b7c-320">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="19b7c-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-321">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19b7c-322">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="19b7c-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="19b7c-323">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="19b7c-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="19b7c-324">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="19b7c-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="19b7c-325">Если запустить приложение и щелкнуть ссылку **Mvc Movie**, возникает ошибка наподобие следующей:</span><span class="sxs-lookup"><span data-stu-id="19b7c-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-327">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="19b7c-328">Вам необходимо создать базу данных. Для этого вы используете функцию [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="19b7c-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="19b7c-329">С помощью миграций можно создать базу данных, соответствующую модели данных, и обновлять схему базы данных при изменении модели.</span><span class="sxs-lookup"><span data-stu-id="19b7c-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="19b7c-330">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="19b7c-330">Initial migration</span></span>

<span data-ttu-id="19b7c-331">В этом разделе выполняются следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="19b7c-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="19b7c-332">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-332">Add an initial migration.</span></span>
* <span data-ttu-id="19b7c-333">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="19b7c-335">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="19b7c-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="19b7c-337">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="19b7c-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="19b7c-338">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="19b7c-339">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="19b7c-340">Аргумент `Initial` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="19b7c-341">Можно использовать любое имя, но по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="19b7c-342">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="19b7c-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="19b7c-343">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-344">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="19b7c-345">Команда `ef migrations add InitialCreate` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="19b7c-346">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="19b7c-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="19b7c-347">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="19b7c-348">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="19b7c-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="19b7c-349">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="19b7c-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="19b7c-350">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="19b7c-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="19b7c-351">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="19b7c-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="19b7c-352">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="19b7c-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="19b7c-353">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="19b7c-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19b7c-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19b7c-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19b7c-355">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="19b7c-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="19b7c-356">Рассмотрим следующий метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="19b7c-357">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="19b7c-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="19b7c-358">`MvcMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="19b7c-359">Контекст данных (`MvcMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="19b7c-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="19b7c-360">Контекст данных указывает сущности, которые включаются в модель данных:</span><span class="sxs-lookup"><span data-stu-id="19b7c-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="19b7c-361">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="19b7c-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="19b7c-362">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="19b7c-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="19b7c-363">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="19b7c-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="19b7c-364">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="19b7c-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="19b7c-365">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="19b7c-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19b7c-366">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="19b7c-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="19b7c-367">Вы создаете контекст базы данных и регистрируете его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="19b7c-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="19b7c-368">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="19b7c-368">Test the app</span></span>

* <span data-ttu-id="19b7c-369">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="19b7c-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="19b7c-370">Если вы получите исключение базы данных, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="19b7c-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="19b7c-371">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="19b7c-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="19b7c-372">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-372">Test the **Create** link.</span></span> <span data-ttu-id="19b7c-373">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="19b7c-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="19b7c-374">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="19b7c-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="19b7c-375">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="19b7c-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="19b7c-376">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="19b7c-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="19b7c-377">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="19b7c-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="19b7c-378">Проверьте класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="19b7c-379">Выделенный выше код демонстрирует добавление контекста базы данных фильмов в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="19b7c-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="19b7c-380">`services.AddDbContext<MvcMovieContext>(options =>` задает используемую базу данных и строку подключения.</span><span class="sxs-lookup"><span data-stu-id="19b7c-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="19b7c-381">`=>` — это [лямбда-оператор](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="19b7c-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="19b7c-382">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="19b7c-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="19b7c-383">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="19b7c-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="19b7c-384">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="19b7c-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="19b7c-385">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="19b7c-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="19b7c-386">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="19b7c-387">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="19b7c-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="19b7c-388">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="19b7c-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="19b7c-389">Такой подход обеспечивает более эффективную проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="19b7c-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="19b7c-390">При создании методов и представлений в этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классами `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="19b7c-391">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="19b7c-392">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="19b7c-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="19b7c-393">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="19b7c-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="19b7c-394">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="19b7c-395">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="19b7c-396">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="19b7c-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="19b7c-397">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="19b7c-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="19b7c-398">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="19b7c-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="19b7c-399">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="19b7c-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="19b7c-400">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="19b7c-401">Изучите содержимое файла *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="19b7c-402">Указав оператор `@model` в начале файла представления, вы можете задать тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="19b7c-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="19b7c-403">При создании контроллера movie следующий оператор `@model` был автоматически добавлен в начало файла *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="19b7c-404">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="19b7c-405">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="19b7c-406">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="19b7c-407">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="19b7c-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="19b7c-408">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="19b7c-409">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="19b7c-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="19b7c-410">При создании контроллера movies механизм формирования шаблонов автоматически включает следующий оператор `@model` в начало файла *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="19b7c-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="19b7c-411">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="19b7c-412">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="19b7c-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="19b7c-413">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19b7c-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="19b7c-414">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции:</span><span class="sxs-lookup"><span data-stu-id="19b7c-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19b7c-415">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="19b7c-415">Additional resources</span></span>

* [<span data-ttu-id="19b7c-416">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="19b7c-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="19b7c-417">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="19b7c-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="19b7c-418">[Назад: добавление представления](adding-view.md)
> [Далее: работа с базой данных](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="19b7c-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
