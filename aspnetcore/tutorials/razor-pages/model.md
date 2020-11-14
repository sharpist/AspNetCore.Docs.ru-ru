---
title: 'Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core'
author: rick-anderson
description: 'Часть 2 серии руководств по Razor Pages.'
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050931"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="cf7a1-103">Часть 2. Добавление модели в приложение Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf7a1-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="cf7a1-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="cf7a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="cf7a1-105">В этом разделе добавляются классы для управления фильмами.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="cf7a1-106">Классы модели приложения используются в [Entity Framework Core](/ef/core) (EF Core) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="cf7a1-107">EF Core —это объектно-реляционный сопоставитель (ORM), упрощающий получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="cf7a1-108">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cf7a1-109">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cf7a1-110">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="cf7a1-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-112">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cf7a1-113">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-113">Name the folder *Models*.</span></span>

<span data-ttu-id="cf7a1-114">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-114">Right click the *Models* folder.</span></span> <span data-ttu-id="cf7a1-115">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="cf7a1-116">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cf7a1-118">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cf7a1-119">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-120">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cf7a1-121">На панели решений щелкните проект **RazorPagesMovie** правой кнопкой мыши, выберите пункт **Добавить**>**Новая папка**. Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="cf7a1-122">Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл...**</span><span class="sxs-lookup"><span data-stu-id="cf7a1-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="cf7a1-123">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cf7a1-124">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cf7a1-125">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cf7a1-126">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cf7a1-127">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cf7a1-128">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="cf7a1-128">Scaffold the movie model</span></span>

<span data-ttu-id="cf7a1-129">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cf7a1-130">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-132">Создайте папку *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cf7a1-133">Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить**  > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cf7a1-134">Назовите папку *Movies*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-134">Name the folder *Movies*</span></span>

<span data-ttu-id="cf7a1-135">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="cf7a1-137">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="cf7a1-139">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cf7a1-140">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="cf7a1-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cf7a1-141">В строке **Класс контекста данных** щелкните значок плюса **+** и измените сгенерированное имя RazorPagesMovie. **Models**.RazorPagesMovieContext на RazorPagesMovie. **Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cf7a1-142">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cf7a1-143">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cf7a1-144">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-144">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/3/arp.png)

<span data-ttu-id="cf7a1-146">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cf7a1-148">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="cf7a1-149">Установка средства формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cf7a1-150">**Для Windows** : Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cf7a1-151">**Для macOS и Linux** : Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-152">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cf7a1-153">Создайте папку *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cf7a1-154">Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить**  > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cf7a1-155">Назовите папку *Movies*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-155">Name the folder *Movies*</span></span>

<span data-ttu-id="cf7a1-156">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить** > **New Scaffolding...** (Создать шаблон...).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="cf7a1-158">В диалоговом окне **New Scaffolding** (Новый шаблон) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cf7a1-160">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cf7a1-161">В раскрывающемся списке **Класс модели** выберите или введите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="cf7a1-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cf7a1-162">В строке **Класс контекста данных** введите имя нового класса RazorPagesMovie. **Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cf7a1-163">[Это изменение](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) не требуется.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cf7a1-164">Оно создает класс контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cf7a1-165">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-165">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="cf7a1-167">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="cf7a1-168">Добавление средств EF</span><span class="sxs-lookup"><span data-stu-id="cf7a1-168">Add EF tools</span></span>

<span data-ttu-id="cf7a1-169">Выполните следующую команду .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="cf7a1-170">Приведенная выше команда добавляет средства Entity Framework Core для .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="cf7a1-171">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="cf7a1-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-173">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cf7a1-174">*Pages/Movies* : Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cf7a1-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cf7a1-176">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="cf7a1-176">Updated</span></span>

* <span data-ttu-id="cf7a1-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-177">*Startup.cs*</span></span>

<span data-ttu-id="cf7a1-178">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-179">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cf7a1-180">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cf7a1-181">*Pages/Movies* : Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cf7a1-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cf7a1-183">Обновленные возможности</span><span class="sxs-lookup"><span data-stu-id="cf7a1-183">Updated</span></span>

* <span data-ttu-id="cf7a1-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-184">*Startup.cs*</span></span>

<span data-ttu-id="cf7a1-185">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cf7a1-187">В процессе формирования шаблонов создаются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="cf7a1-188">*Pages/Movies* : Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="cf7a1-189">В следующем разделе приводится описание созданных файлов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cf7a1-190">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="cf7a1-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-192">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cf7a1-193">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-193">Add an initial migration.</span></span>
* <span data-ttu-id="cf7a1-194">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="cf7a1-195">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cf7a1-197">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-199">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="cf7a1-200">В результате выполнения предыдущих команд выводится следующее предупреждение: "Для десятичного столбца Price в типе сущности Movie не указан тип.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="cf7a1-201">Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="cf7a1-202">С помощью метода 'HasColumnType()' явно укажите тип столбца SQL Server, который может вместить все значения".</span><span class="sxs-lookup"><span data-stu-id="cf7a1-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="cf7a1-203">Это предупреждение можно игнорировать. Оно будет устранено в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="cf7a1-204">Команда миграции формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="cf7a1-205">Схема создается на основе модели, указанной в `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="cf7a1-206">Аргумент `InitialCreate` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="cf7a1-207">Можно использовать любое имя, однако по соглашению выбирается имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="cf7a1-208">Команда `update` выполняет метод `Up` в миграциях, которые не были применены.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="cf7a1-209">В этом случае `update` запускает метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs* , который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cf7a1-211">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="cf7a1-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cf7a1-212">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cf7a1-213">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cf7a1-214">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cf7a1-215">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cf7a1-216">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cf7a1-217">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cf7a1-218">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="cf7a1-219">`RazorPagesMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cf7a1-220">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cf7a1-221">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cf7a1-222">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cf7a1-223">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cf7a1-224">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cf7a1-225">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cf7a1-226">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="cf7a1-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cf7a1-228">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-229">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cf7a1-230">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cf7a1-231">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="cf7a1-231">Test the app</span></span>

* <span data-ttu-id="cf7a1-232">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cf7a1-233">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cf7a1-234">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cf7a1-235">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-235">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cf7a1-237">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cf7a1-238">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cf7a1-239">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cf7a1-240">Протестируйте ссылки **Изменить** , **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="cf7a1-241">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf7a1-242">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cf7a1-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cf7a1-243">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cf7a1-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf7a1-244">В этом разделе добавляются классы для управления фильмами в кроссплатформенной [базе данных SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="cf7a1-245">Приложения, созданные на основе шаблона ASP.NET Core, используют базу данных SQLite.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="cf7a1-246">Эти классы этой модели приложений используются в [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) для работы с базой данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="cf7a1-247">EF Core —это платформа объектно-реляционного сопоставления (ORM), которая упрощает получение доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="cf7a1-248">Эти классы моделей называются классами POCO (от plain old CLR objects — "старые добрые объекты CLR"), так как они не зависят от EF Core.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cf7a1-249">Эти классы определяют свойства данных, которые хранятся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cf7a1-250">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="cf7a1-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-252">Щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cf7a1-253">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-253">Name the folder *Models*.</span></span>

<span data-ttu-id="cf7a1-254">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-254">Right click the *Models* folder.</span></span> <span data-ttu-id="cf7a1-255">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="cf7a1-256">Присвойте классу имя **Movie**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cf7a1-258">Добавьте папку с именем *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cf7a1-259">Добавьте класс в папку *Models* с именем *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-260">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cf7a1-261">В обозревателе решений щелкните проект **RazorPagesMovie** правой кнопкой мыши, выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cf7a1-262">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="cf7a1-263">Щелкните папку *Models* правой кнопкой мыши и выберите пункт **Добавить**>**Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cf7a1-264">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cf7a1-265">Выберите на левой панели пункт **Общие**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cf7a1-266">В центральной области выберите **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cf7a1-267">Назовите класс **Movie** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cf7a1-268">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cf7a1-269">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="cf7a1-269">Scaffold the movie model</span></span>

<span data-ttu-id="cf7a1-270">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cf7a1-271">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-273">Создайте папку *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cf7a1-274">Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить**  > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cf7a1-275">Назовите папку *Movies*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-275">Name the folder *Movies*</span></span>

<span data-ttu-id="cf7a1-276">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="cf7a1-278">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="cf7a1-280">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="cf7a1-281">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="cf7a1-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cf7a1-282">В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cf7a1-283">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-283">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arp.png)

<span data-ttu-id="cf7a1-285">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cf7a1-287">Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs* , *Startup.cs* и *.csproj* файлов).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="cf7a1-288">**Для Windows** : Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cf7a1-289">**Для macOS и Linux** : Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-290">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cf7a1-291">Создайте папку *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cf7a1-292">Щелкните правой кнопкой мыши папку *Pages* и выберите **Добавить**  > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cf7a1-293">Назовите папку *Movies*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-293">Name the folder *Movies*</span></span>

<span data-ttu-id="cf7a1-294">Щелкните правой кнопкой мыши папку *Pages/Movies* и выберите **Добавить**>**New Scaffolded Item** (Создать шаблонный элемент).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/scaMac.png)

<span data-ttu-id="cf7a1-296">В диалоговом окне **Add New Scaffolding** (Добавление нового шаблона) выберите **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cf7a1-298">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cf7a1-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cf7a1-299">В раскрывающемся списке **Класс модели** выберите или введите **Фильм**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="cf7a1-300">В строке **Класс контекста данных** введите или выберите **RazorPagesMovieContext**. Это приведет к созданию класса контекста базы данных с правильным пространством имен.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="cf7a1-301">В этом случае он будет **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cf7a1-302">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-302">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arpMac.png)

<span data-ttu-id="cf7a1-304">Файл *appsettings.json* обновляется с указанием строки подключения, используемой для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="cf7a1-305">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="cf7a1-306">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="cf7a1-306">Files created</span></span>

* <span data-ttu-id="cf7a1-307">*Pages/Movies* : Create, Delete, Details, Edit и Index.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cf7a1-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="cf7a1-309">Обновляемые файлы</span><span class="sxs-lookup"><span data-stu-id="cf7a1-309">File updated</span></span>

* <span data-ttu-id="cf7a1-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cf7a1-310">*Startup.cs*</span></span>

<span data-ttu-id="cf7a1-311">В следующем разделе приводится описание созданных и обновленных файлов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cf7a1-312">Первоначальная миграция</span><span class="sxs-lookup"><span data-stu-id="cf7a1-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf7a1-314">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cf7a1-315">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-315">Add an initial migration.</span></span>
* <span data-ttu-id="cf7a1-316">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="cf7a1-317">В меню **Сервис**  последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cf7a1-319">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="cf7a1-320">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cf7a1-321">Схема создается на основе модели, указанной в `DbContext` (в файле *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cf7a1-322">Аргумент `InitialCreate` используется для присвоения имен миграции.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="cf7a1-323">Можно использовать любое имя, однако по соглашению используется имя, которое описывает миграцию.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="cf7a1-324">Для получения дополнительной информации см. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="cf7a1-325">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="cf7a1-326">Метод `Up` создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-328">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="cf7a1-329">В результате выполнения предыдущих команд выводится следующее предупреждение: " *Для десятичного столбца "Price" в типе сущности "Movie" не указан тип. Это приведет к тому, что значения будут усекаться без вмешательства пользователя, если они не помещаются в значения точности и масштаба по умолчанию. С помощью метода "HasColumnType()" явно укажите тип столбца SQL Server, который может вместить все значения.* " Вы можете игнорировать это предупреждение, оно будет исправлено в следующем учебнике.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf7a1-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf7a1-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cf7a1-331">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="cf7a1-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cf7a1-332">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cf7a1-333">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cf7a1-334">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cf7a1-335">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cf7a1-336">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cf7a1-337">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cf7a1-338">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="cf7a1-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="cf7a1-339">`RazorPagesMovieContext` координирует функции EF Core (Create, Read, Update, Delete и т. д.) для модели `Movie`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cf7a1-340">Контекст данных (`RazorPagesMovieContext`) получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cf7a1-341">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cf7a1-342">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cf7a1-343">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cf7a1-344">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cf7a1-345">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cf7a1-346">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="cf7a1-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf7a1-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf7a1-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cf7a1-348">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf7a1-349">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf7a1-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cf7a1-350">Проверьте метод `Up`.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cf7a1-351">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="cf7a1-351">Test the app</span></span>

* <span data-ttu-id="cf7a1-352">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cf7a1-353">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cf7a1-354">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cf7a1-355">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-355">Test the **Create** link.</span></span>

  ![Страница "Создать"](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cf7a1-357">В поле `Price` нельзя вводить десятичные запятые.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cf7a1-358">Чтобы обеспечить поддержку [проверки jQuery](https://jqueryvalidation.org/) для других языков, кроме английского, используйте вместо десятичной точки запятую (,), а для отображения данных в форматах для других языков, кроме английского, выполните глобализацию приложения.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cf7a1-359">Инструкции по глобализации см. на [сайте GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cf7a1-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cf7a1-360">Протестируйте ссылки **Изменить** , **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="cf7a1-361">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="cf7a1-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf7a1-362">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cf7a1-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cf7a1-363">[Предыдущая статья. Начало работы](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья. Сформированные страницы Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cf7a1-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
