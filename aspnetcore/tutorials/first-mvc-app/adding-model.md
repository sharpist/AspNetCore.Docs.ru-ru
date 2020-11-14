---
title: Часть 4. Добавление модели в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 4 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422757"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b787e-103">Часть 4. Добавление модели в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b787e-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b787e-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="b787e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b787e-105">В этом разделе мы добавим классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="b787e-106">Эти классы будут представлять уровень **м** одели в приложении **M** VC.</span><span class="sxs-lookup"><span data-stu-id="b787e-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="b787e-107">Эти классы используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="b787e-108">EF Core —это платформа объектно реляционного сопоставления (ORM), которая позволяет упростить необходимый код доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="b787e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="b787e-109">Создаваемые вами классы моделей называются классами POCO (от **P** lain **O** ld **C** LR **O**  — старые добрые объекты CLR), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="b787e-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b787e-110">Эти классы просто определяют свойства данных, которые будут храниться в базе данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="b787e-111">Работая с этим учебником, вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="b787e-112">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="b787e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-114">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="b787e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="b787e-115">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b787e-117">Добавьте файл *Movie.cs* в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="b787e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b787e-119">Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="b787e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="b787e-120">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="b787e-121">Обновите файл *Movie.cs* , используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="b787e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="b787e-122">Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="b787e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="b787e-123">Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="b787e-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="b787e-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="b787e-124">With this attribute:</span></span>

* <span data-ttu-id="b787e-125">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="b787e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="b787e-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="b787e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="b787e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="b787e-128">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="b787e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-130">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="b787e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b787e-132">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="b787e-133">Приведенная выше команда добавляет поставщик EF Core для SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b787e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="b787e-134">Пакет поставщика устанавливает пакет EF Core в качестве зависимости.</span><span class="sxs-lookup"><span data-stu-id="b787e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="b787e-135">Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-137">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b787e-138">В меню **Проект** выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b787e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="b787e-139">В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**.</span><span class="sxs-lookup"><span data-stu-id="b787e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="b787e-140">Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="b787e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="b787e-142">Откроется диалоговое окно **Выбор проектов** , в котором будет выбран проект `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="b787e-143">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="b787e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="b787e-144">Появится диалоговое окно **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="b787e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="b787e-145">Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.</span><span class="sxs-lookup"><span data-stu-id="b787e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="b787e-146">Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="b787e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="b787e-147">Создание класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-147">Create a database context class</span></span>

<span data-ttu-id="b787e-148">Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="b787e-149">Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="b787e-150">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="b787e-150">Create a *Data* folder.</span></span>

<span data-ttu-id="b787e-151">Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="b787e-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="b787e-152">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="b787e-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b787e-153">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b787e-154">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="b787e-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="b787e-155">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-155">Register the database context</span></span>

<span data-ttu-id="b787e-156">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b787e-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b787e-157">Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="b787e-158">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="b787e-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b787e-159">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="b787e-160">В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b787e-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="b787e-161">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b787e-162">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b787e-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-164">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="b787e-165">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b787e-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b787e-166">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b787e-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="b787e-167">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-167">Add a database connection string</span></span>

<span data-ttu-id="b787e-168">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="b787e-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-170">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="b787e-171">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="b787e-172">Формирования шаблона страниц фильмов</span><span class="sxs-lookup"><span data-stu-id="b787e-172">Scaffold movie pages</span></span>

<span data-ttu-id="b787e-173">Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="b787e-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-175">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="b787e-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="b787e-177">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b787e-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="b787e-179">Выполните необходимые действия в диалоговом окне **Добавление контроллера** :</span><span class="sxs-lookup"><span data-stu-id="b787e-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="b787e-180">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="b787e-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="b787e-181">**Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="b787e-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Добавление контекста данных](adding-model/_static/dc5.png)

* <span data-ttu-id="b787e-183">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b787e-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="b787e-184">**Имя контроллера:** оставьте имя по умолчанию ( *MoviesController* )</span><span class="sxs-lookup"><span data-stu-id="b787e-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="b787e-185">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="b787e-185">Select **Add**</span></span>

<span data-ttu-id="b787e-186">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="b787e-186">Visual Studio creates:</span></span>

* <span data-ttu-id="b787e-187">контроллер фильмов ( *Controllers/MoviesController.cs* );</span><span class="sxs-lookup"><span data-stu-id="b787e-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="b787e-188">файлы представления Razor для страниц Create, Delete, Details, Edit и Index ( *Views/Movies/\*.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="b787e-189">Автоматическое создание этих файлов называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="b787e-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="b787e-191">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="b787e-192">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="b787e-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="b787e-193">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-194">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b787e-195">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="b787e-196">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="b787e-197">Сформированные страницы пока использовать нельзя, так как база данных не существует.</span><span class="sxs-lookup"><span data-stu-id="b787e-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="b787e-198">Если запустить приложение и щелкнуть ссылку **Movie App** , появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.</span><span class="sxs-lookup"><span data-stu-id="b787e-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="b787e-199">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="b787e-199">Initial migration</span></span>

<span data-ttu-id="b787e-200">Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="b787e-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="b787e-201">Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-203">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="b787e-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="b787e-204">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b787e-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="b787e-205">`Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="b787e-206">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b787e-207">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="b787e-208">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="b787e-209">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="b787e-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="b787e-210">`Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="b787e-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="b787e-211">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="b787e-212">Команда обновления базы данных выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="b787e-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="b787e-213">"Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="b787e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="b787e-214">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b787e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="b787e-215">С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="b787e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="b787e-216">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-217">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="b787e-218">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="b787e-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="b787e-219">`ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="b787e-220">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b787e-221">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="b787e-222">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="b787e-223">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="b787e-224">`ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="b787e-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="b787e-225">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="b787e-226">Класс InitialCreate</span><span class="sxs-lookup"><span data-stu-id="b787e-226">The InitialCreate class</span></span>

<span data-ttu-id="b787e-227">Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="b787e-228">Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="b787e-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="b787e-229">Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.</span><span class="sxs-lookup"><span data-stu-id="b787e-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="b787e-230">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="b787e-230">Test the app</span></span>

* <span data-ttu-id="b787e-231">Запустите приложение и щелкните ссылку **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="b787e-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="b787e-232">Если возникнет исключение наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="b787e-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-234">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="b787e-235">возможно, вы пропустили [шаг миграции](#migration).</span><span class="sxs-lookup"><span data-stu-id="b787e-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="b787e-236">Протестируйте страницу **создания**.</span><span class="sxs-lookup"><span data-stu-id="b787e-236">Test the **Create** page.</span></span> <span data-ttu-id="b787e-237">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="b787e-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b787e-238">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="b787e-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b787e-239">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b787e-240">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="b787e-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b787e-241">Протестируйте страницы **редактирования** , **сведений** и **удаления**.</span><span class="sxs-lookup"><span data-stu-id="b787e-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="b787e-242">Внедрение зависимостей в контроллере</span><span class="sxs-lookup"><span data-stu-id="b787e-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-244">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="b787e-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b787e-245">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="b787e-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="b787e-246">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="b787e-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-247">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b787e-248">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="b787e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="b787e-249">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="b787e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="b787e-250">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="b787e-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="b787e-251">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="b787e-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="b787e-252">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="b787e-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="b787e-253">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="b787e-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="b787e-254">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="b787e-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="b787e-255">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b787e-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="b787e-256">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="b787e-257">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="b787e-258">Такой подход обеспечивает проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="b787e-259">В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="b787e-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="b787e-260">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="b787e-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="b787e-261">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="b787e-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="b787e-262">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="b787e-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="b787e-263">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="b787e-264">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="b787e-265">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="b787e-266">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b787e-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="b787e-267">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="b787e-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="b787e-268">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="b787e-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="b787e-269">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="b787e-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="b787e-270">Изучите содержимое файла *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="b787e-271">Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="b787e-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="b787e-272">При создании контроллера movie был включен следующий оператор `@model`:</span><span class="sxs-lookup"><span data-stu-id="b787e-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="b787e-273">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="b787e-274">Объект `Model` является строго типизированным.</span><span class="sxs-lookup"><span data-stu-id="b787e-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="b787e-275">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="b787e-276">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="b787e-277">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="b787e-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="b787e-278">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="b787e-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="b787e-279">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="b787e-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="b787e-280">При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="b787e-281">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b787e-282">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="b787e-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="b787e-283">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="b787e-284">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b787e-285">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b787e-285">Additional resources</span></span>

* [<span data-ttu-id="b787e-286">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="b787e-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="b787e-287">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="b787e-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="b787e-288">[Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="b787e-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="b787e-289">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="b787e-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-291">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="b787e-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="b787e-292">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b787e-294">Добавьте файл *Movie.cs* в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="b787e-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-295">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b787e-296">Щелкните правой кнопкой мыши папку *Models* и выберите пункты **Добавить** > **Новый класс** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="b787e-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="b787e-297">Назовите файл *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="b787e-298">Обновите файл *Movie.cs* , используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="b787e-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="b787e-299">Класс `Movie` содержит поле `Id`, которое требуется базе данных в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="b787e-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="b787e-300">Атрибут <xref:System.ComponentModel.DataAnnotations.DataType> для `ReleaseDate` указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="b787e-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="b787e-301">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="b787e-301">With this attribute:</span></span>

* <span data-ttu-id="b787e-302">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="b787e-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="b787e-303">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="b787e-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="b787e-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="b787e-305">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="b787e-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-307">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="b787e-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b787e-309">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="b787e-310">Приведенная выше команда добавляет поставщик EF Core для SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b787e-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="b787e-311">Пакет поставщика устанавливает пакет EF Core в качестве зависимости.</span><span class="sxs-lookup"><span data-stu-id="b787e-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="b787e-312">Дополнительные пакеты устанавливаются автоматически на этапе формирования шаблонов далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-314">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b787e-315">В меню **Проект** выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b787e-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="b787e-316">В поле **Поиск** в правом верхнем углу введите `Microsoft.EntityFrameworkCore.SQLite` и нажмите клавишу **возврата**.</span><span class="sxs-lookup"><span data-stu-id="b787e-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="b787e-317">Выберите соответствующий пакет NuGet и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="b787e-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Добавление пакета NuGet Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="b787e-319">Откроется диалоговое окно **Выбор проектов** , в котором будет выбран проект `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="b787e-320">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="b787e-320">Press the **Ok** button.</span></span>

<span data-ttu-id="b787e-321">Появится диалоговое окно **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="b787e-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="b787e-322">Просмотрите лицензии по своему усмотрению, а затем нажмите кнопку **Принять**.</span><span class="sxs-lookup"><span data-stu-id="b787e-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="b787e-323">Повторите приведенные выше шаги, чтобы установить следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="b787e-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="b787e-324">Создание класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-324">Create a database context class</span></span>

<span data-ttu-id="b787e-325">Класс контекста базы данных необходим в целях координации функциональных возможностей EF Core (создание, чтение, обновление, удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="b787e-326">Контекст базы данных наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) и определяет сущности, которые необходимо включить в модель данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="b787e-327">Создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="b787e-327">Create a *Data* folder.</span></span>

<span data-ttu-id="b787e-328">Добавьте файл *Data/MvcMovieContext.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="b787e-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="b787e-329">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="b787e-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b787e-330">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b787e-331">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="b787e-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="b787e-332">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-332">Register the database context</span></span>

<span data-ttu-id="b787e-333">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b787e-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b787e-334">Службы (например, контекст базы данных EF Core) должны регистрироваться с помощью внедрения зависимостей во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="b787e-335">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="b787e-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b787e-336">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="b787e-337">В этом разделе контекст базы данных регистрируется в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b787e-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="b787e-338">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b787e-339">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b787e-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-341">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="b787e-342">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b787e-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b787e-343">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b787e-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="b787e-344">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="b787e-344">Add a database connection string</span></span>

<span data-ttu-id="b787e-345">Добавьте строку подключения в файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="b787e-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-347">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="b787e-348">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="b787e-349">Формирования шаблона страниц фильмов</span><span class="sxs-lookup"><span data-stu-id="b787e-349">Scaffold movie pages</span></span>

<span data-ttu-id="b787e-350">Используйте средство формирования шаблонов, чтобы создать страницы для операций создания, чтения, обновления и удаления (CRUD) для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="b787e-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-352">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="b787e-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="b787e-354">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b787e-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="b787e-356">Выполните необходимые действия в диалоговом окне **Добавление контроллера** :</span><span class="sxs-lookup"><span data-stu-id="b787e-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="b787e-357">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="b787e-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="b787e-358">**Класс контекста данных:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="b787e-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Добавление контекста данных](adding-model/_static/dc3.png)

* <span data-ttu-id="b787e-360">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b787e-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="b787e-361">**Имя контроллера:** оставьте имя по умолчанию ( *MoviesController* )</span><span class="sxs-lookup"><span data-stu-id="b787e-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="b787e-362">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="b787e-362">Select **Add**</span></span>

<span data-ttu-id="b787e-363">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="b787e-363">Visual Studio creates:</span></span>

* <span data-ttu-id="b787e-364">контроллер фильмов ( *Controllers/MoviesController.cs* );</span><span class="sxs-lookup"><span data-stu-id="b787e-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="b787e-365">файлы представления Razor для страниц Create, Delete, Details, Edit и Index ( *Views/Movies/\*.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="b787e-366">Автоматическое создание этих файлов называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="b787e-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="b787e-368">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="b787e-369">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="b787e-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="b787e-370">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-371">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b787e-372">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="b787e-373">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="b787e-374">Сформированные страницы пока использовать нельзя, так как база данных не существует.</span><span class="sxs-lookup"><span data-stu-id="b787e-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="b787e-375">Если запустить приложение и щелкнуть ссылку **Movie App** , появится сообщение об ошибке *Невозможно открыть базу данных* или *отсутствует таблица: Movie*.</span><span class="sxs-lookup"><span data-stu-id="b787e-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="b787e-376">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="b787e-376">Initial migration</span></span>

<span data-ttu-id="b787e-377">Создайте базу данных с помощью функции [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="b787e-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="b787e-378">Миграции — это набор средств, позволяющих создавать и обновлять базы данных в соответствии с моделью данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-380">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="b787e-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="b787e-381">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b787e-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="b787e-382">`Add-Migration InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="b787e-383">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b787e-384">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="b787e-385">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="b787e-386">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="b787e-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="b787e-387">`Update-Database`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="b787e-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="b787e-388">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="b787e-389">Команда обновления базы данных выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="b787e-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="b787e-390">"Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="b787e-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="b787e-391">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b787e-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="b787e-392">С помощью метода HasColumnType() явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="b787e-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="b787e-393">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-394">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="b787e-395">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="b787e-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="b787e-396">`ef migrations add InitialCreate`. Создает файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="b787e-397">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b787e-398">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="b787e-399">Так как это первая миграция, созданный класс содержит код для создания схемы базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="b787e-400">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="b787e-401">`ef database update`. Обновляет базу данных до последней миграции, созданной предыдущей командой.</span><span class="sxs-lookup"><span data-stu-id="b787e-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="b787e-402">Эта команда выполняет метод `Up` в файле *Migrations/{метка_времени}_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="b787e-403">Класс InitialCreate</span><span class="sxs-lookup"><span data-stu-id="b787e-403">The InitialCreate class</span></span>

<span data-ttu-id="b787e-404">Изучите файл миграции *Migrations/{метка_времени}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="b787e-405">Метод `Up` создает таблицу Movie и настраивает `Id` в качестве первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="b787e-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="b787e-406">Метод `Down` отменяет изменения схемы, внесенные миграцией `Up`.</span><span class="sxs-lookup"><span data-stu-id="b787e-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="b787e-407">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="b787e-407">Test the app</span></span>

* <span data-ttu-id="b787e-408">Запустите приложение и щелкните ссылку **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="b787e-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="b787e-409">Если возникнет исключение наподобие следующего:</span><span class="sxs-lookup"><span data-stu-id="b787e-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-411">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="b787e-412">возможно, вы пропустили [шаг миграции](#migration).</span><span class="sxs-lookup"><span data-stu-id="b787e-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="b787e-413">Протестируйте страницу **создания**.</span><span class="sxs-lookup"><span data-stu-id="b787e-413">Test the **Create** page.</span></span> <span data-ttu-id="b787e-414">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="b787e-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b787e-415">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="b787e-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b787e-416">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b787e-417">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="b787e-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b787e-418">Протестируйте страницы **редактирования** , **сведений** и **удаления**.</span><span class="sxs-lookup"><span data-stu-id="b787e-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="b787e-419">Внедрение зависимостей в контроллере</span><span class="sxs-lookup"><span data-stu-id="b787e-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-421">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="b787e-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b787e-422">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="b787e-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="b787e-423">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="b787e-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-424">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b787e-425">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="b787e-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="b787e-426">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="b787e-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="b787e-427">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="b787e-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="b787e-428">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="b787e-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="b787e-429">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="b787e-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="b787e-430">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="b787e-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="b787e-431">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="b787e-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="b787e-432">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b787e-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="b787e-433">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="b787e-434">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="b787e-435">Такой подход обеспечивает проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="b787e-436">В этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классом `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="b787e-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="b787e-437">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="b787e-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="b787e-438">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="b787e-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="b787e-439">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="b787e-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="b787e-440">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="b787e-441">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="b787e-442">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="b787e-443">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b787e-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="b787e-444">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="b787e-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="b787e-445">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="b787e-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="b787e-446">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="b787e-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="b787e-447">Изучите содержимое файла *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="b787e-448">Оператор `@model` в начале файла представления задает тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="b787e-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="b787e-449">При создании контроллера movie был включен следующий оператор `@model`:</span><span class="sxs-lookup"><span data-stu-id="b787e-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="b787e-450">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="b787e-451">Объект `Model` является строго типизированным.</span><span class="sxs-lookup"><span data-stu-id="b787e-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="b787e-452">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="b787e-453">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="b787e-454">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="b787e-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="b787e-455">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="b787e-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="b787e-456">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="b787e-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="b787e-457">При создании контроллера movies механизм формирования шаблонов включил следующий оператор `@model` в начало файла *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="b787e-458">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b787e-459">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="b787e-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="b787e-460">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="b787e-461">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b787e-462">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b787e-462">Additional resources</span></span>

* [<span data-ttu-id="b787e-463">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="b787e-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="b787e-464">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="b787e-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="b787e-465">[Назад: добавление представления](adding-view.md)
> [Далее: работа с SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="b787e-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="b787e-466">Добавление класса модели данных</span><span class="sxs-lookup"><span data-stu-id="b787e-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-468">Щелкните правой кнопкой мыши папку *Models* и выберите пункт **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="b787e-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="b787e-469">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="b787e-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-470">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b787e-471">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="b787e-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b787e-472">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="b787e-472">Scaffold the movie model</span></span>

<span data-ttu-id="b787e-473">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="b787e-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b787e-474">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="b787e-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-476">В **Обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="b787e-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![представление указанного выше шага](adding-model/_static/add_controller21.png)

<span data-ttu-id="b787e-478">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC с представлениями, использующий Entity Framework > Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b787e-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Диалоговое окно "Добавление шаблона"](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="b787e-480">Выполните необходимые действия в диалоговом окне **Добавление контроллера** :</span><span class="sxs-lookup"><span data-stu-id="b787e-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="b787e-481">**Класс модели:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="b787e-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="b787e-482">**Класс контекста данных:** щелкните значок **+** и добавьте класс **MvcMovie.Models.MvcMovieContext** по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b787e-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Добавление контекста данных](adding-model/_static/dc.png)

* <span data-ttu-id="b787e-484">**Представления:** оставьте флажки, установленные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b787e-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="b787e-485">**Имя контроллера:** оставьте имя по умолчанию ( *MoviesController* )</span><span class="sxs-lookup"><span data-stu-id="b787e-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="b787e-486">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="b787e-486">Select **Add**</span></span>

![Диалоговое окно "Добавление контроллера"](adding-model/_static/add_controller2.png)

<span data-ttu-id="b787e-488">Visual Studio создаст следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="b787e-488">Visual Studio creates:</span></span>

* <span data-ttu-id="b787e-489">[класс контекста базы данных](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core ( *Data/MvcMovieContext.cs* );</span><span class="sxs-lookup"><span data-stu-id="b787e-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="b787e-490">контроллер фильмов ( *Controllers/MoviesController.cs* );</span><span class="sxs-lookup"><span data-stu-id="b787e-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="b787e-491">файлы представления Razor для страниц Create, Delete, Details, Edit и Index ( *Views/Movies/\*.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="b787e-492">Автоматическое создание контекста базы данных и методов и представлений действий [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется *формированием шаблонов*.</span><span class="sxs-lookup"><span data-stu-id="b787e-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b787e-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b787e-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b787e-494">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="b787e-495">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="b787e-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b787e-496">В Linux экспортируйте путь к средству формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="b787e-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="b787e-497">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b787e-498">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b787e-499">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="b787e-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="b787e-500">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="b787e-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b787e-501">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b787e-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="b787e-502">Если запустить приложение и щелкнуть ссылку **Mvc Movie** , возникает ошибка наподобие следующей:</span><span class="sxs-lookup"><span data-stu-id="b787e-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-504">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="b787e-505">Вам необходимо создать базу данных. Для этого вы используете функцию [миграций](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="b787e-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="b787e-506">С помощью миграций можно создать базу данных, соответствующую модели данных, и обновлять схему базы данных при изменении модели.</span><span class="sxs-lookup"><span data-stu-id="b787e-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b787e-507">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="b787e-507">Initial migration</span></span>

<span data-ttu-id="b787e-508">В этом разделе выполняются следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="b787e-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="b787e-509">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-509">Add an initial migration.</span></span>
* <span data-ttu-id="b787e-510">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b787e-512">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="b787e-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Меню PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="b787e-514">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="b787e-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="b787e-515">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="b787e-516">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="b787e-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="b787e-517">Аргумент `Initial` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="b787e-518">Можно использовать любое имя, но по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="b787e-519">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="b787e-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="b787e-520">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-521">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="b787e-522">Команда `ef migrations add InitialCreate` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="b787e-523">Схема базы данных создается на основе модели, указанной в классе `MvcMovieContext` (в файле *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="b787e-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="b787e-524">Аргумент `InitialCreate` — это имя миграции.</span><span class="sxs-lookup"><span data-stu-id="b787e-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b787e-525">Можно использовать любое имя, но по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="b787e-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b787e-526">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="b787e-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b787e-527">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b787e-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b787e-528">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="b787e-529">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="b787e-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b787e-530">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="b787e-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b787e-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b787e-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b787e-532">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b787e-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="b787e-533">Рассмотрим следующий метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b787e-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b787e-534">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="b787e-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="b787e-535">`MvcMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b787e-536">Контекст данных (`MvcMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b787e-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b787e-537">Контекст данных указывает сущности, которые включаются в модель данных:</span><span class="sxs-lookup"><span data-stu-id="b787e-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="b787e-538">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="b787e-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b787e-539">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="b787e-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b787e-540">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="b787e-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b787e-541">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b787e-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b787e-542">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b787e-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b787e-543">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b787e-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b787e-544">Вы создаете контекст базы данных и регистрируете его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b787e-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b787e-545">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="b787e-545">Test the app</span></span>

* <span data-ttu-id="b787e-546">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="b787e-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b787e-547">Если вы получите исключение базы данных, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="b787e-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b787e-548">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="b787e-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b787e-549">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b787e-549">Test the **Create** link.</span></span> <span data-ttu-id="b787e-550">Введите и отправьте данные.</span><span class="sxs-lookup"><span data-stu-id="b787e-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b787e-551">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="b787e-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b787e-552">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="b787e-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b787e-553">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="b787e-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b787e-554">Протестируйте ссылки **Изменить** , **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="b787e-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="b787e-555">Проверьте класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b787e-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="b787e-556">Выделенный выше код демонстрирует добавление контекста базы данных фильмов в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="b787e-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="b787e-557">`services.AddDbContext<MvcMovieContext>(options =>` задает используемую базу данных и строку подключения.</span><span class="sxs-lookup"><span data-stu-id="b787e-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="b787e-558">`=>` — это [лямбда-оператор](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="b787e-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="b787e-559">Откройте файл *Controllers/MoviesController.cs* и изучите его конструктор:</span><span class="sxs-lookup"><span data-stu-id="b787e-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b787e-560">Этот конструктор применяет [внедрение зависимостей](xref:fundamentals/dependency-injection) для внедрения контекста базы данных (`MvcMovieContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="b787e-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="b787e-561">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="b787e-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="b787e-562">Строго типизированные модели и ключевое слово @model</span><span class="sxs-lookup"><span data-stu-id="b787e-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="b787e-563">Ранее в этом руководстве вы ознакомились с передачей данных или объектов из контроллера в представление с использованием словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="b787e-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="b787e-564">Словарь `ViewData` представляет собой динамический объект, который реализует удобный механизм позднего связывания для передачи информации в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="b787e-565">Модель MVC также поддерживает передачу строго типизированных объектов модели в представление.</span><span class="sxs-lookup"><span data-stu-id="b787e-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="b787e-566">Такой подход обеспечивает более эффективную проверку кода во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b787e-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="b787e-567">При создании методов и представлений в этом подходе используется механизм формирования шаблонов (то есть передачи строго типизированной модели) с представлениями и классами `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="b787e-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="b787e-568">Изучите созданный метод `Details` в файле *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="b787e-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="b787e-569">Параметр `id` обычно передается в качестве данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="b787e-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="b787e-570">Например, `https://localhost:5001/movies/details/1` задает:</span><span class="sxs-lookup"><span data-stu-id="b787e-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="b787e-571">Контроллер `movies` (первый сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="b787e-572">Действие `details` (второй сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="b787e-573">Идентификатор 1 (последний сегмент URL-адреса).</span><span class="sxs-lookup"><span data-stu-id="b787e-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="b787e-574">Также можно передать `id` с помощью строки запроса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="b787e-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="b787e-575">Параметр `id` определяется как [тип, допускающий значение NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), в случае, если не указано значение идентификатора.</span><span class="sxs-lookup"><span data-stu-id="b787e-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="b787e-576">[Лямбда-выражение](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) передается в `FirstOrDefaultAsync` для выбора записей фильмов, которые соответствуют данным маршрута или значению строки запроса.</span><span class="sxs-lookup"><span data-stu-id="b787e-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="b787e-577">Если фильм найден, экземпляр модели `Movie` передается в представление `Details`:</span><span class="sxs-lookup"><span data-stu-id="b787e-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="b787e-578">Изучите содержимое файла *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="b787e-579">Указав оператор `@model` в начале файла представления, вы можете задать тип объекта, который будет ожидаться представлением.</span><span class="sxs-lookup"><span data-stu-id="b787e-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="b787e-580">При создании контроллера movie следующий оператор `@model` был автоматически добавлен в начало файла *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="b787e-581">Эта директива `@model` обеспечивает доступ к фильму, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b787e-582">Например, в представлении *Details.cshtml* код передает каждое поле фильма во вспомогательные функции HTML `DisplayNameFor` и `DisplayFor` со строго типизированным объектом `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="b787e-583">Методы `Create` и `Edit` и представления также передают объект модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="b787e-584">Изучите представление *Index.cshtml* и метод `Index` в контроллере Movies.</span><span class="sxs-lookup"><span data-stu-id="b787e-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="b787e-585">Обратите внимание на то, как в коде создается объект `List` при вызове метода `View`.</span><span class="sxs-lookup"><span data-stu-id="b787e-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="b787e-586">Код передает список `Movies` из метода действия `Index` в представление:</span><span class="sxs-lookup"><span data-stu-id="b787e-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="b787e-587">При создании контроллера movies механизм формирования шаблонов автоматически включает следующий оператор `@model` в начало файла *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b787e-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="b787e-588">Эта директива `@model` обеспечивает доступ к списку фильмов, который контроллер передал в представление с использованием строго типизированного объекта `Model`.</span><span class="sxs-lookup"><span data-stu-id="b787e-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b787e-589">Например, в представлении *Index.cshtml* код циклически перебирает фильмы с использованием оператора `foreach` для строго типизированного объекта `Model`:</span><span class="sxs-lookup"><span data-stu-id="b787e-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="b787e-590">Поскольку объект `Model` является строго типизированным (как и объект `IEnumerable<Movie>`), каждый элемент в цикле получает тип `Movie`.</span><span class="sxs-lookup"><span data-stu-id="b787e-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="b787e-591">Помимо прочих преимуществ, это означает, что выполняется проверка кода во время компиляции:</span><span class="sxs-lookup"><span data-stu-id="b787e-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b787e-592">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b787e-592">Additional resources</span></span>

* [<span data-ttu-id="b787e-593">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="b787e-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="b787e-594">Глобализация и локализация</span><span class="sxs-lookup"><span data-stu-id="b787e-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="b787e-595">[Назад: добавление представления](adding-view.md)
> [Далее: работа с базой данных](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="b787e-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
