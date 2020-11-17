---
title: Часть 2. Добавление модели
author: rick-anderson
description: Часть 2 серии руководств по Razor Pages. В этом разделе содержатся сведения о добавлении классов моделей.
ms.author: riande
ms.date: 11/11/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550698"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="e1cce-104">Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1cce-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e1cce-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="e1cce-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e1cce-106">В этом разделе добавляются классы для управления фильмами в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="e1cce-107">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e1cce-108">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e1cce-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="e1cce-109">Вы напишете классы моделей, а затем EF Core создаст базу данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="e1cce-110">Эти классы моделей называются классами POCO (от "**P** lain-**O** ld **C** LR **O** bjects" — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e1cce-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="e1cce-111">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e1cce-112">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e1cce-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e1cce-113">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e1cce-115">В **Обозревателе решений** щелкните правой кнопкой мыши проект *RazorPagesMovie* и выберите пункт **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e1cce-116">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="e1cce-117">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="e1cce-118">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="e1cce-119">Присвойте классу имя *Movie*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="e1cce-120">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-121">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-122">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-123">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-124">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-124">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-125">Пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-126">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e1cce-128">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="e1cce-129">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e1cce-130">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-131">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-132">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-133">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-134">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-134">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-135">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-136">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e1cce-137">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e1cce-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e1cce-138">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-138">Add a database context class</span></span>

1. <span data-ttu-id="e1cce-139">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="e1cce-140">В папке *Data* добавьте файл с именем *RazorPagesMovieContext.cs* с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="e1cce-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="e1cce-141">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e1cce-142">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e1cce-143">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e1cce-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e1cce-144">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-144">Add a database connection string</span></span>

<span data-ttu-id="e1cce-145">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e1cce-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e1cce-146">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-146">Register the database context</span></span>

1. <span data-ttu-id="e1cce-147">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="e1cce-148">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e1cce-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-149">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e1cce-150">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="e1cce-151">Щелкните папку *Models* и выберите пункт **Add** (Добавить)>**Создать файл...** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="e1cce-152">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e1cce-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="e1cce-153">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="e1cce-154">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="e1cce-155">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="e1cce-156">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-157">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-158">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-159">`[DataType(DataType.Date)]`: Атрибут [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-160">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-160">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-161">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-162">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e1cce-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e1cce-164">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e1cce-165">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e1cce-165">Scaffold the movie model</span></span>

<span data-ttu-id="e1cce-166">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e1cce-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e1cce-167">То есть средство формирования шаблонов создает страницы для операций Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление) (CRUD) для модели movie.</span><span class="sxs-lookup"><span data-stu-id="e1cce-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e1cce-169">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e1cce-170">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e1cce-171">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e1cce-172">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e1cce-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/sca.png)

1. <span data-ttu-id="e1cce-174">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

1. <span data-ttu-id="e1cce-176">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e1cce-177">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="e1cce-178">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="e1cce-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="e1cce-179">В диалоговом окне **Добавление контекста данных** создается имя класса *RazorPagesMovie.Data.RazorPagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="e1cce-180">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-180">Select **Add**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="e1cce-182">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e1cce-184">Откройте в командной оболочке папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e1cce-185">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e1cce-186">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e1cce-187">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e1cce-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="e1cce-188">Параметр</span><span class="sxs-lookup"><span data-stu-id="e1cce-188">Option</span></span>               | <span data-ttu-id="e1cce-189">Описание:</span><span class="sxs-lookup"><span data-stu-id="e1cce-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e1cce-190">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e1cce-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e1cce-191">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e1cce-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e1cce-192">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e1cce-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e1cce-193">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e1cce-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e1cce-194">Добавляет `_ValidationScriptsPartial` для страниц Edit (Изменение) и Create (Создание)</span><span class="sxs-lookup"><span data-stu-id="e1cce-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e1cce-195">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e1cce-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e1cce-196">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e1cce-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e1cce-197">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e1cce-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e1cce-198">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e1cce-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e1cce-199">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e1cce-200">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e1cce-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-201">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e1cce-202">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e1cce-203">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e1cce-204">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e1cce-205">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="e1cce-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

1. <span data-ttu-id="e1cce-207">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="e1cce-209">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e1cce-210">В классе **DbContext для использования: строки** назовите класс *RazorPagesMovie.Data.Data.RazorPagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="e1cce-211">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-211">Select **Finish**.</span></span>

   ![Изображение из предыдущих инструкций.](model/_static/5/arpMac.png)

<span data-ttu-id="e1cce-213">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e1cce-214">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e1cce-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e1cce-215">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e1cce-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e1cce-216">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e1cce-217">`IWebHostEnvironment` внедрен, поэтому приложение может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e1cce-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e1cce-218">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e1cce-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-220">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e1cce-221">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e1cce-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e1cce-223">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e1cce-223">Updated</span></span>

* <span data-ttu-id="e1cce-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-224">*Startup.cs*</span></span>

<span data-ttu-id="e1cce-225">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e1cce-227">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e1cce-228">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e1cce-229">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-230">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e1cce-231">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e1cce-232">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e1cce-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e1cce-234">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e1cce-234">Updated</span></span>

* <span data-ttu-id="e1cce-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-235">*Startup.cs*</span></span>

<span data-ttu-id="e1cce-236">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="e1cce-237">Использование командыCreate для создания начальной схемы базы данных с помощью функции миграции EF</span><span class="sxs-lookup"><span data-stu-id="e1cce-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="e1cce-238">Функция миграции в Entity Framework Core предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="e1cce-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="e1cce-239">Создание начальной схемы базы данных с помощью команды Create.</span><span class="sxs-lookup"><span data-stu-id="e1cce-239">Create the initial database schema.</span></span>
* <span data-ttu-id="e1cce-240">Поэтапное обновление схемы базы данных, чтобы она соответствовала модели данных приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="e1cce-241">Существующие данные в базе данных сохраняются.</span><span class="sxs-lookup"><span data-stu-id="e1cce-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-243">В этом разделе окно **Консоль диспетчера пакетов** (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e1cce-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="e1cce-244">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-244">Add an initial migration.</span></span>
* <span data-ttu-id="e1cce-245">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="e1cce-246">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Меню PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="e1cce-248">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e1cce-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-249">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="e1cce-250">Выполните следующие команды интерфейса командной строки .NET:</span><span class="sxs-lookup"><span data-stu-id="e1cce-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="e1cce-251">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e1cce-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e1cce-252">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e1cce-253">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e1cce-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e1cce-254">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e1cce-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e1cce-255">Команда `migrations` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e1cce-256">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e1cce-257">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="e1cce-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e1cce-258">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e1cce-259">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="e1cce-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e1cce-260">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e1cce-262">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e1cce-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e1cce-263">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e1cce-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e1cce-264">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e1cce-265">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e1cce-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e1cce-266">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e1cce-267">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e1cce-268">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e1cce-269">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e1cce-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e1cce-270">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление), для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e1cce-271">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e1cce-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e1cce-272">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e1cce-273">Представленный выше код создает свойство [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="e1cce-274">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e1cce-275">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e1cce-276">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e1cce-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e1cce-277">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1cce-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-278">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e1cce-279">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="e1cce-280">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e1cce-280">Test the app</span></span>

1. <span data-ttu-id="e1cce-281">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="e1cce-282">Если вы получаете следующую ошибку:</span><span class="sxs-lookup"><span data-stu-id="e1cce-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="e1cce-283">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e1cce-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="e1cce-284">Протестируйте ссылку **Create** (Создание).</span><span class="sxs-lookup"><span data-stu-id="e1cce-284">Test the **Create** link.</span></span>

   ![Экран "Страница Create"](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="e1cce-286">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e1cce-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e1cce-287">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e1cce-288">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e1cce-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="e1cce-289">Протестируйте ссылки **Edit** (Изменение), **Details** (Сведения) и **Delete (Удаление)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e1cce-290">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1cce-291">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e1cce-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e1cce-292">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e1cce-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e1cce-293">В этом разделе добавляются классы для управления фильмами.</span><span class="sxs-lookup"><span data-stu-id="e1cce-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="e1cce-294">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e1cce-295">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e1cce-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="e1cce-296">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e1cce-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e1cce-297">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e1cce-298">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e1cce-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e1cce-299">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-301">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e1cce-302">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-302">Name the folder *Models*.</span></span>

<span data-ttu-id="e1cce-303">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="e1cce-304">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="e1cce-305">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-305">Name the class **Movie**.</span></span>

<span data-ttu-id="e1cce-306">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-307">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-308">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-309">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-310">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-310">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-311">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-312">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e1cce-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e1cce-315">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e1cce-316">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e1cce-317">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-318">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-319">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-320">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-321">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-321">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-322">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-323">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e1cce-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e1cce-325">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e1cce-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e1cce-326">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-326">Add a database context class</span></span>

* <span data-ttu-id="e1cce-327">В проекте *RazorPagesMovie* создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="e1cce-328">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e1cce-329">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e1cce-330">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e1cce-331">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e1cce-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e1cce-332">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-332">Add a database connection string</span></span>

<span data-ttu-id="e1cce-333">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e1cce-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e1cce-334">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-334">Register the database context</span></span>

<span data-ttu-id="e1cce-335">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e1cce-336">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-337">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e1cce-338">В **окне инструментов решения** щелкните проект **RazorPagesMovie**, а затем выберите **Add**(Добавить)> **Создать папку...** . Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e1cce-339">Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**</span><span class="sxs-lookup"><span data-stu-id="e1cce-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e1cce-340">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e1cce-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e1cce-341">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e1cce-342">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e1cce-343">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e1cce-344">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-345">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-346">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-347">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-348">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-348">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-349">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-350">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e1cce-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e1cce-352">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e1cce-353">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e1cce-353">Scaffold the movie model</span></span>

<span data-ttu-id="e1cce-354">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e1cce-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e1cce-355">То есть средство формирования шаблонов создает страницы для операций Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление) (CRUD) для модели movie.</span><span class="sxs-lookup"><span data-stu-id="e1cce-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-357">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e1cce-358">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e1cce-359">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="e1cce-360">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e1cce-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="e1cce-362">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="e1cce-364">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e1cce-365">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e1cce-366">В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie.**Models**.RazorPagesMovieContext на RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e1cce-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e1cce-367">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="e1cce-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e1cce-368">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e1cce-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e1cce-369">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-369">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="e1cce-371">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e1cce-373">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e1cce-374">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e1cce-375">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e1cce-376">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e1cce-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e1cce-377">Параметр</span><span class="sxs-lookup"><span data-stu-id="e1cce-377">Option</span></span>               | <span data-ttu-id="e1cce-378">Описание:</span><span class="sxs-lookup"><span data-stu-id="e1cce-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e1cce-379">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e1cce-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e1cce-380">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e1cce-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e1cce-381">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e1cce-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e1cce-382">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e1cce-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e1cce-383">Добавляет `_ValidationScriptsPartial` для страниц Edit (Изменение) и Create (Создание)</span><span class="sxs-lookup"><span data-stu-id="e1cce-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e1cce-384">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e1cce-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e1cce-385">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e1cce-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e1cce-386">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e1cce-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e1cce-387">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e1cce-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e1cce-388">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="e1cce-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e1cce-389">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e1cce-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-390">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e1cce-391">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e1cce-392">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e1cce-393">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="e1cce-394">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="e1cce-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="e1cce-396">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e1cce-398">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e1cce-399">В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e1cce-400">В строке **Класс контекста данных** введите имя нового класса RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e1cce-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e1cce-401">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="e1cce-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e1cce-402">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e1cce-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e1cce-403">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-403">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="e1cce-405">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="e1cce-406">Добавление средств EF</span><span class="sxs-lookup"><span data-stu-id="e1cce-406">Add EF tools</span></span>

<span data-ttu-id="e1cce-407">Выполните следующую команду .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="e1cce-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="e1cce-408">Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="e1cce-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="e1cce-409">Дополнительные сведения см. в статье [Справочник по средствам Entity Framework Core в .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="e1cce-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e1cce-410">Использование SQLite для среды разработки и SQL Server для рабочей среды</span><span class="sxs-lookup"><span data-stu-id="e1cce-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e1cce-411">Если выбрана SQLite, код, созданный шаблоном, будет готов к разработке.</span><span class="sxs-lookup"><span data-stu-id="e1cce-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e1cce-412">В примере кода ниже показано, как внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в среду загрузки.</span><span class="sxs-lookup"><span data-stu-id="e1cce-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e1cce-413">Интерфейс `IWebHostEnvironment` внедрен, поэтому `ConfigureServices` может использовать SQLite в среде разработки и SQL Server в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e1cce-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e1cce-414">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e1cce-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-416">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e1cce-417">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e1cce-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e1cce-419">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e1cce-419">Updated</span></span>

* <span data-ttu-id="e1cce-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-420">*Startup.cs*</span></span>

<span data-ttu-id="e1cce-421">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-422">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e1cce-423">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e1cce-424">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e1cce-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e1cce-426">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="e1cce-426">Updated</span></span>

* <span data-ttu-id="e1cce-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-427">*Startup.cs*</span></span>

<span data-ttu-id="e1cce-428">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e1cce-430">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e1cce-431">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e1cce-432">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e1cce-433">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="e1cce-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-435">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e1cce-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e1cce-436">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-436">Add an initial migration.</span></span>
* <span data-ttu-id="e1cce-437">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="e1cce-438">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e1cce-440">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e1cce-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-441">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e1cce-442">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e1cce-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e1cce-443">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e1cce-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e1cce-444">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e1cce-445">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e1cce-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e1cce-446">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e1cce-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e1cce-447">Команда миграции формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e1cce-448">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e1cce-449">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="e1cce-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e1cce-450">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e1cce-451">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="e1cce-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e1cce-452">В этом случае команда `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e1cce-454">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e1cce-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e1cce-455">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e1cce-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e1cce-456">С помощью внедрения зависимостей службы, например контекст базы данных EF Core, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e1cce-457">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e1cce-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="e1cce-458">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e1cce-459">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e1cce-460">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e1cce-461">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e1cce-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e1cce-462">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление), для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e1cce-463">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e1cce-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e1cce-464">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e1cce-465">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e1cce-466">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e1cce-467">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e1cce-468">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e1cce-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e1cce-469">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1cce-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-470">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e1cce-471">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e1cce-472">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e1cce-472">Test the app</span></span>

* <span data-ttu-id="e1cce-473">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e1cce-474">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="e1cce-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e1cce-475">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e1cce-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e1cce-476">Протестируйте ссылку **Create** (Создание).</span><span class="sxs-lookup"><span data-stu-id="e1cce-476">Test the **Create** link.</span></span>

  ![Экран "Страница Create"](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="e1cce-478">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e1cce-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e1cce-479">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e1cce-480">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e1cce-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e1cce-481">Протестируйте ссылки **Edit** (Изменение), **Details** (Сведения) и **Delete (Удаление)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e1cce-482">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1cce-483">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e1cce-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e1cce-484">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e1cce-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1cce-485">В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="e1cce-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e1cce-486">Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite.</span><span class="sxs-lookup"><span data-stu-id="e1cce-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e1cce-487">Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e1cce-488">EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="e1cce-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e1cce-489">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="e1cce-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e1cce-490">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e1cce-491">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e1cce-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e1cce-492">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-494">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e1cce-495">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-495">Name the folder *Models*.</span></span>

<span data-ttu-id="e1cce-496">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="e1cce-497">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="e1cce-498">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-498">Name the class **Movie**.</span></span>

<span data-ttu-id="e1cce-499">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-500">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-501">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-502">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-503">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-503">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-504">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-505">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e1cce-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e1cce-508">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e1cce-509">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e1cce-510">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-511">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-512">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-513">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-514">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-514">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-515">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-516">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e1cce-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e1cce-518">Добавление пакетов NuGet и средств EF</span><span class="sxs-lookup"><span data-stu-id="e1cce-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e1cce-519">Добавление класса контекста для базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-519">Add a database context class</span></span>

<span data-ttu-id="e1cce-520">В проекте RazorPagesMovie создайте папку с именем *Data*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="e1cce-521">Добавьте следующий класс `RazorPagesMovieContext` в папку *Data*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e1cce-522">Представленный выше код создает свойство `DbSet` для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e1cce-523">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e1cce-524">Код не будет компилироваться, пока на более позднем этапе не будут добавлены зависимости.</span><span class="sxs-lookup"><span data-stu-id="e1cce-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e1cce-525">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-525">Add a database connection string</span></span>

<span data-ttu-id="e1cce-526">Добавьте строку подключения в файл *appsettings.json* , как это показано в следующем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="e1cce-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e1cce-527">Добавьте необходимые пакеты NuGet</span><span class="sxs-lookup"><span data-stu-id="e1cce-527">Add required NuGet packages</span></span>

<span data-ttu-id="e1cce-528">Выполните следующую команду .NET Core CLI, чтобы добавить в проект SQLite и CodeGeneration.Design:</span><span class="sxs-lookup"><span data-stu-id="e1cce-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="e1cce-529">Пакет `Microsoft.VisualStudio.Web.CodeGeneration.Design` необходим для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e1cce-530">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="e1cce-530">Register the database context</span></span>

<span data-ttu-id="e1cce-531">Добавьте следующие инструкции `using` в начало файла *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e1cce-532">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e1cce-533">Соберите проект как проверку на ошибки.</span><span class="sxs-lookup"><span data-stu-id="e1cce-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-534">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e1cce-535">В **окне инструментов решения** щелкните проект *RazorPagesMovie*, а затем выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e1cce-536">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="e1cce-537">Щелкните папку *Models* и выберите пункт **Add** (Добавить) > **Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e1cce-538">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e1cce-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e1cce-539">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e1cce-540">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e1cce-541">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e1cce-542">Добавьте в класс `Movie` следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="e1cce-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e1cce-543">Класс `Movie` содержит:</span><span class="sxs-lookup"><span data-stu-id="e1cce-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="e1cce-544">Поле `ID` является обязательным для первичного ключа базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e1cce-545">`[DataType(DataType.Date)]`: Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (`Date`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e1cce-546">С этим атрибутом:</span><span class="sxs-lookup"><span data-stu-id="e1cce-546">With this attribute:</span></span>

  * <span data-ttu-id="e1cce-547">пользователю не требуется вводить сведения о времени в поле даты.</span><span class="sxs-lookup"><span data-stu-id="e1cce-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e1cce-548">Отображается только дата, а не время.</span><span class="sxs-lookup"><span data-stu-id="e1cce-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e1cce-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) рассматриваются в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="e1cce-550">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e1cce-551">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="e1cce-551">Scaffold the movie model</span></span>

<span data-ttu-id="e1cce-552">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="e1cce-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e1cce-553">То есть средство формирования шаблонов создает страницы для операций Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление) (CRUD) для модели movie.</span><span class="sxs-lookup"><span data-stu-id="e1cce-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-555">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e1cce-556">Щелкните правой кнопкой мыши папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e1cce-557">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="e1cce-558">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e1cce-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="e1cce-560">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="e1cce-562">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e1cce-563">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e1cce-564">В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e1cce-565">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-565">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arp.png)

<span data-ttu-id="e1cce-567">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e1cce-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1cce-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e1cce-569">Откройте в командном окне папку проекта, что содержит файлы *Program.cs*, *Startup.cs* и *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e1cce-570">**Для Windows**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e1cce-571">**Для macOS и Linux**: Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e1cce-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e1cce-572">В следующей таблице представлены параметры генератора кода ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e1cce-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e1cce-573">Параметр</span><span class="sxs-lookup"><span data-stu-id="e1cce-573">Option</span></span>               | <span data-ttu-id="e1cce-574">Описание:</span><span class="sxs-lookup"><span data-stu-id="e1cce-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e1cce-575">Имя модели</span><span class="sxs-lookup"><span data-stu-id="e1cce-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e1cce-576">Класс `DbContext` для использования.</span><span class="sxs-lookup"><span data-stu-id="e1cce-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e1cce-577">Использование макета по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e1cce-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e1cce-578">Относительный путь к папке выходных данных для создания представлений</span><span class="sxs-lookup"><span data-stu-id="e1cce-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e1cce-579">Добавляет `_ValidationScriptsPartial` для страниц Edit (Изменение) и Create (Создание)</span><span class="sxs-lookup"><span data-stu-id="e1cce-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e1cce-580">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`:</span><span class="sxs-lookup"><span data-stu-id="e1cce-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e1cce-581">См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e1cce-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-582">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e1cce-583">Создайте (с помощью Create) папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e1cce-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e1cce-584">Щелкните папку *Pages* и выберите **Add**(Добавить) > **New Folder**(Новая папка).</span><span class="sxs-lookup"><span data-stu-id="e1cce-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e1cce-585">Назовите папку *Movies*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="e1cce-586">Щелкните папку *Pages/Movies* и выберите **Add**(Добавить) > **New Scaffolded Item**(Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="e1cce-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="e1cce-588">В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e1cce-590">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e1cce-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e1cce-591">В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e1cce-592">В строке **Data context class** (Класс контекста данных) введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="e1cce-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="e1cce-593">В этом случае он будет **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e1cce-594">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-594">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="e1cce-596">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e1cce-597">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="e1cce-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e1cce-598">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="e1cce-598">Files created</span></span>

* <span data-ttu-id="e1cce-599">*Pages/Movie*: Create(Создание), Delete(Удаление), Details (Сведения), Edit (Изменение) и Index(Индекс).</span><span class="sxs-lookup"><span data-stu-id="e1cce-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e1cce-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e1cce-601">Обновляемые файлы</span><span class="sxs-lookup"><span data-stu-id="e1cce-601">File updated</span></span>

* <span data-ttu-id="e1cce-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e1cce-602">*Startup.cs*</span></span>

<span data-ttu-id="e1cce-603">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e1cce-604">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="e1cce-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1cce-606">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="e1cce-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e1cce-607">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-607">Add an initial migration.</span></span>
* <span data-ttu-id="e1cce-608">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="e1cce-609">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="e1cce-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e1cce-611">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="e1cce-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e1cce-612">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e1cce-613">Схема создается на основе модели, указанной в `DbContext`в файле *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="e1cce-614">Аргумент `InitialCreate` используется для присвоения имен миграции.</span><span class="sxs-lookup"><span data-stu-id="e1cce-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e1cce-615">Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e1cce-616">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e1cce-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e1cce-617">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="e1cce-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e1cce-618">Метод `Up` создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-619">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e1cce-620">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e1cce-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e1cce-621">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="e1cce-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e1cce-622">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1cce-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e1cce-623">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="e1cce-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e1cce-624">Пропустите это предупреждение, так как оно будет рассмотрено на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="e1cce-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1cce-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1cce-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e1cce-626">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="e1cce-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e1cce-627">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e1cce-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e1cce-628">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e1cce-629">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="e1cce-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e1cce-630">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="e1cce-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e1cce-631">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e1cce-632">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e1cce-633">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="e1cce-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e1cce-634">`RazorPagesMovieContext` координирует функции EF Core, такие как Create (Создание), Read (Чтение), Update (Обновление) и Delete (Удаление) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e1cce-635">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e1cce-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e1cce-636">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e1cce-637">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="e1cce-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e1cce-638">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="e1cce-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e1cce-639">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="e1cce-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e1cce-640">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="e1cce-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e1cce-641">При локальной разработке [система конфигурации](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1cce-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e1cce-642">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e1cce-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e1cce-643">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="e1cce-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e1cce-644">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="e1cce-644">Test the app</span></span>

* <span data-ttu-id="e1cce-645">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e1cce-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e1cce-646">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="e1cce-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e1cce-647">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e1cce-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e1cce-648">Протестируйте ссылку **Create** (Создание).</span><span class="sxs-lookup"><span data-stu-id="e1cce-648">Test the **Create** link.</span></span>

  ![Экран "Страница Create"](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e1cce-650">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="e1cce-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e1cce-651">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="e1cce-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e1cce-652">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e1cce-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e1cce-653">Протестируйте ссылки **Edit** (Изменение), **Details** (Сведения) и **Delete (Удаление)** .</span><span class="sxs-lookup"><span data-stu-id="e1cce-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e1cce-654">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="e1cce-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1cce-655">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e1cce-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e1cce-656">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e1cce-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
