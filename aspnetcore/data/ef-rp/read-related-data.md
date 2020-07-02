---
title: Часть 6. Razor Pages с EF Core в ASP.NET Core — чтение связанных данных
author: rick-anderson
description: Часть 6 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 14b28f04f4077cb5622858dad1bd18b81b198f3d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405800"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="fa803-103">Часть 6. Razor Pages с EF Core в ASP.NET Core — чтение связанных данных</span><span class="sxs-lookup"><span data-stu-id="fa803-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="fa803-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="fa803-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa803-105">Этот учебник посвящен чтению и отображению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="fa803-106">Связанными называются данные, которые EF Core загружает в свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="fa803-107">На следующих рисунках изображены готовые страницы для этого руководства:</span><span class="sxs-lookup"><span data-stu-id="fa803-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="fa803-110">Безотложная, явная и отложенная загрузка</span><span class="sxs-lookup"><span data-stu-id="fa803-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="fa803-111">Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:</span><span class="sxs-lookup"><span data-stu-id="fa803-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="fa803-112">[Безотложная загрузка](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="fa803-113">Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="fa803-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="fa803-114">При чтении сущности извлекаются ее связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="fa803-115">Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="fa803-116">Для некоторых типов безотложной загрузки EF Core выдает несколько запросов.</span><span class="sxs-lookup"><span data-stu-id="fa803-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="fa803-117">Отправка нескольких запросов может оказаться эффективнее, чем выполнение одного большого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa803-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="fa803-118">Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="fa803-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="fa803-120">Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:</span><span class="sxs-lookup"><span data-stu-id="fa803-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="fa803-121">Один запрос для основного запроса</span><span class="sxs-lookup"><span data-stu-id="fa803-121">One query for the main query</span></span> 
  * <span data-ttu-id="fa803-122">Один запрос для каждого "края" коллекции в дереве загрузки.</span><span class="sxs-lookup"><span data-stu-id="fa803-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="fa803-123">Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="fa803-124">Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="fa803-125">Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.</span><span class="sxs-lookup"><span data-stu-id="fa803-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="fa803-127">**Примечание.** EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="fa803-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="fa803-128">Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="fa803-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="fa803-129">[Явная загрузка](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="fa803-130">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="fa803-131">Нужно написать код, извлекающий связанные данные, когда они необходимы.</span><span class="sxs-lookup"><span data-stu-id="fa803-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="fa803-132">Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="fa803-133">При явной загрузке код указывает, какие свойства навигации нужно загрузить.</span><span class="sxs-lookup"><span data-stu-id="fa803-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="fa803-134">Для выполнения явной загрузки используется метод `Load`.</span><span class="sxs-lookup"><span data-stu-id="fa803-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="fa803-135">Пример:</span><span class="sxs-lookup"><span data-stu-id="fa803-135">For example:</span></span>

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="fa803-137">[Отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="fa803-138">[В EF Core версии 2.1 добавлена отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="fa803-139">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="fa803-140">При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="fa803-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="fa803-141">Запрос к базе данных отправляется при каждом первом обращении к свойству навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="fa803-142">Создание страниц курсов</span><span class="sxs-lookup"><span data-stu-id="fa803-142">Create Course pages</span></span>

<span data-ttu-id="fa803-143">Сущность `Course` включает в себя свойство навигации, содержащее связанную сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="fa803-145">Чтобы отобразить имя кафедры, которой назначен курс, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="fa803-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="fa803-146">Загрузите связанную сущность `Department` в свойство навигации `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="fa803-147">Получите имя из свойства `Name` сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="fa803-148">Формирование шаблона для страниц курсов</span><span class="sxs-lookup"><span data-stu-id="fa803-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa803-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa803-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fa803-150">Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="fa803-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="fa803-151">Создайте папку *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="fa803-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="fa803-152">Используйте класс модели `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="fa803-153">Используйте существующий класс контекста вместо создания нового.</span><span class="sxs-lookup"><span data-stu-id="fa803-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa803-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa803-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fa803-155">Создайте папку *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="fa803-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="fa803-156">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц курсов.</span><span class="sxs-lookup"><span data-stu-id="fa803-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="fa803-157">**В Windows:**</span><span class="sxs-lookup"><span data-stu-id="fa803-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="fa803-158">**В Linux или macOS:**</span><span class="sxs-lookup"><span data-stu-id="fa803-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="fa803-159">Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="fa803-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="fa803-160">Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="fa803-161">Метод `Include` задает безотложную загрузку.</span><span class="sxs-lookup"><span data-stu-id="fa803-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="fa803-162">Запустите приложение и выберите ссылку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="fa803-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="fa803-163">В столбце кафедры отображается бесполезный `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="fa803-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="fa803-164">Отображение названия кафедры</span><span class="sxs-lookup"><span data-stu-id="fa803-164">Display the department name</span></span>

<span data-ttu-id="fa803-165">Измените файл Pages/Courses/Index.cshtml.cs, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="fa803-166">Приведенный выше код изменяет свойство `Course` на `Courses` и добавляет `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="fa803-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="fa803-167">`AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="fa803-168">Отслеживать сущности не нужно, так как они не изменяются в текущем контексте.</span><span class="sxs-lookup"><span data-stu-id="fa803-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="fa803-169">Измените файл *Pages/Courses/Index.cshtml*, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="fa803-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="fa803-170">В шаблонный код были внесены следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="fa803-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="fa803-171">Имя свойства `Course` изменилось на `Courses`.</span><span class="sxs-lookup"><span data-stu-id="fa803-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="fa803-172">Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="fa803-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="fa803-173">По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла.</span><span class="sxs-lookup"><span data-stu-id="fa803-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="fa803-174">Однако в нашем случае первичный ключ имеет смысл.</span><span class="sxs-lookup"><span data-stu-id="fa803-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="fa803-175">Изменен столбец **Department** (Кафедра) для отображения названия кафедры.</span><span class="sxs-lookup"><span data-stu-id="fa803-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="fa803-176">Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:</span><span class="sxs-lookup"><span data-stu-id="fa803-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="fa803-177">Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="fa803-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="fa803-179">Загрузка связанных данных с помощью "Select"</span><span class="sxs-lookup"><span data-stu-id="fa803-179">Loading related data with Select</span></span>

<span data-ttu-id="fa803-180">Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`.</span><span class="sxs-lookup"><span data-stu-id="fa803-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="fa803-181">Метод `Select` является альтернативным вариантом, который загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="fa803-182">Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="fa803-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="fa803-183">Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.</span><span class="sxs-lookup"><span data-stu-id="fa803-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="fa803-184">Следующий код загружает связанные данные с помощью метода `Select`:</span><span class="sxs-lookup"><span data-stu-id="fa803-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="fa803-185">Приведенный выше код не возвращает никаких типов сущностей, поэтому отслеживание не выполняется.</span><span class="sxs-lookup"><span data-stu-id="fa803-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="fa803-186">Дополнительные сведения об отслеживании EF см. в разделе [Отслеживание или Отключение отслеживания запросов](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="fa803-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="fa803-187">`CourseViewModel`.</span><span class="sxs-lookup"><span data-stu-id="fa803-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="fa803-188">Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="fa803-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="fa803-189">Создание страниц преподавателей</span><span class="sxs-lookup"><span data-stu-id="fa803-189">Create Instructor pages</span></span>

<span data-ttu-id="fa803-190">В этом разделе формируется шаблон для страниц преподавателей, а затем на страницу указателя преподавателей добавляются связанные курсы и регистрации.</span><span class="sxs-lookup"><span data-stu-id="fa803-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="fa803-191">![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="fa803-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="fa803-192">Эта страница считывает и отображает связанные данные следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fa803-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="fa803-193">Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении).</span><span class="sxs-lookup"><span data-stu-id="fa803-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="fa803-194">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="fa803-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="fa803-195">Безотложная загрузка используется для сущностей `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="fa803-196">Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="fa803-197">В этом случае отображается принадлежность к кабинету для каждого преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="fa803-198">Когда пользователь выбирает преподавателя, отображаются связанные сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="fa803-199">Между сущностями `Instructor` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="fa803-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="fa803-200">Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="fa803-201">В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="fa803-202">Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="fa803-203">Когда пользователь выбирает курс, отображаются связанные данные из сущности `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="fa803-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="fa803-204">На предыдущем изображении отображается имя и оценка учащегося.</span><span class="sxs-lookup"><span data-stu-id="fa803-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="fa803-205">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="fa803-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="fa803-206">Создание модели представления</span><span class="sxs-lookup"><span data-stu-id="fa803-206">Create a view model</span></span>

<span data-ttu-id="fa803-207">На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц.</span><span class="sxs-lookup"><span data-stu-id="fa803-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="fa803-208">Требуется модель представления, которая включает в себя три свойства, представляющие три таблицы.</span><span class="sxs-lookup"><span data-stu-id="fa803-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="fa803-209">Создайте файл *SchoolViewModels/InstructorIndexData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="fa803-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="fa803-210">Формирование шаблона для страниц преподавателей</span><span class="sxs-lookup"><span data-stu-id="fa803-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa803-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa803-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fa803-212">Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="fa803-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="fa803-213">Создайте папку *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="fa803-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="fa803-214">Используйте класс модели `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="fa803-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="fa803-215">Используйте существующий класс контекста вместо создания нового.</span><span class="sxs-lookup"><span data-stu-id="fa803-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa803-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa803-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fa803-217">Создайте папку *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="fa803-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="fa803-218">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц преподавателей.</span><span class="sxs-lookup"><span data-stu-id="fa803-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="fa803-219">**В Windows:**</span><span class="sxs-lookup"><span data-stu-id="fa803-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="fa803-220">**В Linux или macOS:**</span><span class="sxs-lookup"><span data-stu-id="fa803-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="fa803-221">Чтобы увидеть, как выглядит шаблонная страница до ее изменения, запустите приложение и перейдите на страницу преподавателей.</span><span class="sxs-lookup"><span data-stu-id="fa803-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="fa803-222">Измените файл *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="fa803-223">Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="fa803-224">Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa803-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="fa803-225">В коде задается безотложная загрузка для следующих свойств навигации:</span><span class="sxs-lookup"><span data-stu-id="fa803-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="fa803-226">Обратите внимание на то, что методы `Include` и `ThenInclude` повторяются для `CourseAssignments` и `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="fa803-227">Это необходимо для того, чтобы задать безотложную загрузку для двух свойств навигации сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="fa803-228">Приведенный ниже код выполняется при выборе преподавателя (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="fa803-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="fa803-229">Выбранный преподаватель извлекается из списка преподавателей в модели представления.</span><span class="sxs-lookup"><span data-stu-id="fa803-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="fa803-230">Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="fa803-231">Метод `Where` возвращает коллекцию.</span><span class="sxs-lookup"><span data-stu-id="fa803-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="fa803-232">Но в этом случае фильтр выберет одну сущность, поэтому вызывается метод `Single` для преобразования коллекции в одну сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="fa803-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="fa803-233">Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="fa803-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="fa803-234">`CourseAssignments` предоставляет доступ к связанным сущностям `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="fa803-236">Метод `Single` используется для коллекции, когда она содержит всего один элемент.</span><span class="sxs-lookup"><span data-stu-id="fa803-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="fa803-237">Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента.</span><span class="sxs-lookup"><span data-stu-id="fa803-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="fa803-238">Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.</span><span class="sxs-lookup"><span data-stu-id="fa803-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="fa803-239">Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:</span><span class="sxs-lookup"><span data-stu-id="fa803-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="fa803-240">Изменение страницы индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="fa803-240">Update the instructors Index page</span></span>

<span data-ttu-id="fa803-241">Измените файл *Pages/Instructors/Index.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="fa803-242">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="fa803-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="fa803-243">Изменяет директиву `page` с `@page` на `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="fa803-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="fa803-244">`"{id:int?}"` является шаблоном маршрута.</span><span class="sxs-lookup"><span data-stu-id="fa803-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="fa803-245">Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="fa803-246">Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:</span><span class="sxs-lookup"><span data-stu-id="fa803-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="fa803-247">Когда используется директива страницы `@page "{id:int?}"`, URL-адрес имеет следующее значение:</span><span class="sxs-lookup"><span data-stu-id="fa803-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="fa803-248">Добавляет столбец **Office**, в котором отображается `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно NULL.</span><span class="sxs-lookup"><span data-stu-id="fa803-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="fa803-249">Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.</span><span class="sxs-lookup"><span data-stu-id="fa803-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="fa803-250">Добавляет столбец **Courses**, содержащий курсы, которые ведет конкретный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="fa803-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="fa803-251">Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="fa803-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="fa803-252">Добавляет код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя и курса.</span><span class="sxs-lookup"><span data-stu-id="fa803-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="fa803-253">Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="fa803-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="fa803-254">Добавляет новую гиперссылку с меткой **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="fa803-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="fa803-255">Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.</span><span class="sxs-lookup"><span data-stu-id="fa803-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="fa803-256">Добавляет таблицу курсов для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="fa803-257">Добавляет таблицу регистраций учащихся для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="fa803-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="fa803-258">Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="fa803-259">Если `OfficeAssignment` имеет значение NULL, отображается пустая ячейка таблицы.</span><span class="sxs-lookup"><span data-stu-id="fa803-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="fa803-260">Щелкните ссылку **Select** для преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="fa803-261">Стиль строки изменится, и отобразятся курсы, назначенные этому преподавателю.</span><span class="sxs-lookup"><span data-stu-id="fa803-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="fa803-262">Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.</span><span class="sxs-lookup"><span data-stu-id="fa803-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="fa803-264">Использование метода Single</span><span class="sxs-lookup"><span data-stu-id="fa803-264">Using Single</span></span>

<span data-ttu-id="fa803-265">Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:</span><span class="sxs-lookup"><span data-stu-id="fa803-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="fa803-266">Использование `Single` с условием WHERE — это вопрос личных предпочтений.</span><span class="sxs-lookup"><span data-stu-id="fa803-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="fa803-267">Оно не дает преимуществ по сравнению с использованием метода `Where`.</span><span class="sxs-lookup"><span data-stu-id="fa803-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="fa803-268">Явная загрузка</span><span class="sxs-lookup"><span data-stu-id="fa803-268">Explicit loading</span></span>

<span data-ttu-id="fa803-269">Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:</span><span class="sxs-lookup"><span data-stu-id="fa803-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="fa803-270">Предположим, что пользователям редко требуется просматривать зачисления на курс.</span><span class="sxs-lookup"><span data-stu-id="fa803-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="fa803-271">В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе.</span><span class="sxs-lookup"><span data-stu-id="fa803-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="fa803-272">В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.</span><span class="sxs-lookup"><span data-stu-id="fa803-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="fa803-273">Измените файл *Pages/Instructors/Index.cshtml.cs*, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="fa803-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="fa803-274">Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся.</span><span class="sxs-lookup"><span data-stu-id="fa803-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="fa803-275">Если курс выбран, код явной загрузки извлекает следующее:</span><span class="sxs-lookup"><span data-stu-id="fa803-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="fa803-276">Сущности `Enrollment` для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="fa803-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="fa803-277">Сущности `Student` для каждого `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="fa803-278">Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="fa803-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="fa803-279">Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.</span><span class="sxs-lookup"><span data-stu-id="fa803-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="fa803-280">Тестирование приложения.</span><span class="sxs-lookup"><span data-stu-id="fa803-280">Test the app.</span></span> <span data-ttu-id="fa803-281">С точки зрения пользователя приложение работает аналогично предыдущей версии.</span><span class="sxs-lookup"><span data-stu-id="fa803-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fa803-282">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="fa803-282">Next steps</span></span>

<span data-ttu-id="fa803-283">Следующее руководство посвящено обновлению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="fa803-284">[Предыдущий учебник](xref:data/ef-rp/complex-data-model)
>[Следующий учебник](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="fa803-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa803-285">В этом руководстве выполняется чтение и отображение связанных данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="fa803-286">Связанными называются данные, которые EF Core загружает в свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="fa803-287">При возникновении проблем, которые вам не удается устранить, [скачайте или просмотрите готовое приложение.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="fa803-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="fa803-288">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="fa803-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="fa803-289">На следующих рисунках изображены готовые страницы для этого руководства:</span><span class="sxs-lookup"><span data-stu-id="fa803-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="fa803-292">Безотложная, явная и отложенная загрузка связанных данных</span><span class="sxs-lookup"><span data-stu-id="fa803-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="fa803-293">Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:</span><span class="sxs-lookup"><span data-stu-id="fa803-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="fa803-294">[Безотложная загрузка](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="fa803-295">Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="fa803-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="fa803-296">При чтении сущности связанные данные извлекаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="fa803-297">Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="fa803-298">Для некоторых типов безотложной загрузки EF Core выдает несколько запросов.</span><span class="sxs-lookup"><span data-stu-id="fa803-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="fa803-299">Выдача нескольких запросов может оказаться более эффективной по сравнению с отдельными ситуациями в EF6, когда выполнялся отдельный запрос.</span><span class="sxs-lookup"><span data-stu-id="fa803-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="fa803-300">Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="fa803-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="fa803-302">Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:</span><span class="sxs-lookup"><span data-stu-id="fa803-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="fa803-303">Один запрос для основного запроса</span><span class="sxs-lookup"><span data-stu-id="fa803-303">One query for the main query</span></span> 
  * <span data-ttu-id="fa803-304">Один запрос для каждого "края" коллекции в дереве загрузки.</span><span class="sxs-lookup"><span data-stu-id="fa803-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="fa803-305">Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="fa803-306">Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="fa803-307">Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.</span><span class="sxs-lookup"><span data-stu-id="fa803-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="fa803-309">Примечание. EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="fa803-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="fa803-310">Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="fa803-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="fa803-311">[Явная загрузка](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="fa803-312">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="fa803-313">Нужно написать код, извлекающий связанные данные, когда они необходимы.</span><span class="sxs-lookup"><span data-stu-id="fa803-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="fa803-314">Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="fa803-315">При явной загрузке код указывает, какие свойства навигации нужно загрузить.</span><span class="sxs-lookup"><span data-stu-id="fa803-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="fa803-316">Для выполнения явной загрузки используется метод `Load`.</span><span class="sxs-lookup"><span data-stu-id="fa803-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="fa803-317">Пример:</span><span class="sxs-lookup"><span data-stu-id="fa803-317">For example:</span></span>

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="fa803-319">[Отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="fa803-320">[В EF Core версии 2.1 добавлена отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="fa803-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="fa803-321">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="fa803-322">При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="fa803-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="fa803-323">Запрос к базе данных отправляется при каждом первом обращении к свойству навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="fa803-324">Оператор `Select` загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="fa803-325">Создание страницы "Course" (Курс) с отображением названий кафедр</span><span class="sxs-lookup"><span data-stu-id="fa803-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="fa803-326">Сущность "Course" включает в себя свойство навигации, содержащее сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="fa803-327">Сущность `Department` содержит кафедру, которой назначен курс.</span><span class="sxs-lookup"><span data-stu-id="fa803-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="fa803-328">Отображение имени назначенной кафедры в списке курсов:</span><span class="sxs-lookup"><span data-stu-id="fa803-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="fa803-329">Получите свойство `Name` из сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="fa803-330">Сущность `Department` получается из свойства навигации `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="fa803-332">Формирование шаблона для модели Course</span><span class="sxs-lookup"><span data-stu-id="fa803-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa803-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa803-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="fa803-334">Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Course` для класса модели.</span><span class="sxs-lookup"><span data-stu-id="fa803-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa803-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa803-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="fa803-336">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="fa803-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="fa803-337">Предыдущая команда формирует шаблон для модели `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="fa803-338">Откройте проект в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fa803-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="fa803-339">Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="fa803-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="fa803-340">Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="fa803-341">Метод `Include` задает безотложную загрузку.</span><span class="sxs-lookup"><span data-stu-id="fa803-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="fa803-342">Запустите приложение и выберите ссылку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="fa803-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="fa803-343">В столбце кафедры отображается бесполезный `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="fa803-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="fa803-344">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="fa803-345">Предыдущий код добавляет `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="fa803-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="fa803-346">`AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="fa803-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="fa803-347">Отслеживание отсутствует, так как сущности не изменяются в текущем контексте.</span><span class="sxs-lookup"><span data-stu-id="fa803-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="fa803-348">Измените *Pages/Courses/Index.cshtml*, используя выделенную ниже разметку:</span><span class="sxs-lookup"><span data-stu-id="fa803-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="fa803-349">В шаблонный код были внесены следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="fa803-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="fa803-350">Изменен заголовок с "Index" (Индекс) на "Courses" (Курсы).</span><span class="sxs-lookup"><span data-stu-id="fa803-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="fa803-351">Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="fa803-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="fa803-352">По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла.</span><span class="sxs-lookup"><span data-stu-id="fa803-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="fa803-353">Однако в нашем случае первичный ключ имеет смысл.</span><span class="sxs-lookup"><span data-stu-id="fa803-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="fa803-354">Изменен столбец **Department** (Кафедра) для отображения названия кафедры.</span><span class="sxs-lookup"><span data-stu-id="fa803-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="fa803-355">Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:</span><span class="sxs-lookup"><span data-stu-id="fa803-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="fa803-356">Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="fa803-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="fa803-358">Загрузка связанных данных с помощью "Select"</span><span class="sxs-lookup"><span data-stu-id="fa803-358">Loading related data with Select</span></span>

<span data-ttu-id="fa803-359">Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`:</span><span class="sxs-lookup"><span data-stu-id="fa803-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="fa803-360">Оператор `Select` загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="fa803-361">Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="fa803-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="fa803-362">Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.</span><span class="sxs-lookup"><span data-stu-id="fa803-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="fa803-363">Следующий код загружает связанные данные с помощью метода `Select`:</span><span class="sxs-lookup"><span data-stu-id="fa803-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="fa803-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="fa803-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="fa803-365">Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="fa803-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="fa803-366">Создание страницы "Instructors" (Преподаватели) с отображением курсов и дат зачисления</span><span class="sxs-lookup"><span data-stu-id="fa803-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="fa803-367">В этом разделе создается страница "Instructors" (Преподаватели).</span><span class="sxs-lookup"><span data-stu-id="fa803-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="fa803-368">![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="fa803-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="fa803-369">Эта страница считывает и отображает связанные данные следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fa803-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="fa803-370">Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении).</span><span class="sxs-lookup"><span data-stu-id="fa803-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="fa803-371">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="fa803-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="fa803-372">Безотложная загрузка используется для сущностей `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="fa803-373">Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные.</span><span class="sxs-lookup"><span data-stu-id="fa803-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="fa803-374">В этом случае отображается принадлежность к кабинету для каждого преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="fa803-375">Когда пользователь выбирает преподавателя (Harui на предыдущем изображении), отображаются связанные сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="fa803-376">Между сущностями `Instructor` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="fa803-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="fa803-377">Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="fa803-378">В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="fa803-379">Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.</span><span class="sxs-lookup"><span data-stu-id="fa803-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="fa803-380">Когда пользователь выбирает курс ("Chemistry" (Химия) на предыдущем изображении), отображаются связанные данные из сущности `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="fa803-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="fa803-381">На предыдущем изображении отображается имя и оценка учащегося.</span><span class="sxs-lookup"><span data-stu-id="fa803-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="fa803-382">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="fa803-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="fa803-383">Создание модели для представления индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="fa803-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="fa803-384">На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц.</span><span class="sxs-lookup"><span data-stu-id="fa803-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="fa803-385">Создается модель представления, которая включает три сущности, представляющие три таблицы.</span><span class="sxs-lookup"><span data-stu-id="fa803-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="fa803-386">Создайте в папке *SchoolViewModels* файл *InstructorIndexData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="fa803-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="fa803-387">Формирование шаблона для модели "Instructor" (Преподаватель)</span><span class="sxs-lookup"><span data-stu-id="fa803-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa803-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa803-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="fa803-389">Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Instructor` для класса модели.</span><span class="sxs-lookup"><span data-stu-id="fa803-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa803-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa803-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="fa803-391">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="fa803-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="fa803-392">Предыдущая команда формирует шаблон для модели `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="fa803-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="fa803-393">Запустите приложение и перейдите на страницу преподавателей.</span><span class="sxs-lookup"><span data-stu-id="fa803-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="fa803-394">Замените содержимое *Pages/Instructors/Index.cshtml.cs* на следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="fa803-395">Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="fa803-396">Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa803-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="fa803-397">Запрос имеет две операции включения:</span><span class="sxs-lookup"><span data-stu-id="fa803-397">The query has two includes:</span></span>

* <span data-ttu-id="fa803-398">`OfficeAssignment`: отображается в [представлении преподавателей](#IP).</span><span class="sxs-lookup"><span data-stu-id="fa803-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="fa803-399">`CourseAssignments`: вызывает проводимые курсы.</span><span class="sxs-lookup"><span data-stu-id="fa803-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="fa803-400">Изменение страницы индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="fa803-400">Update the instructors Index page</span></span>

<span data-ttu-id="fa803-401">Измените *Pages/Instructors/Index.cshtml*, используя следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="fa803-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="fa803-402">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="fa803-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="fa803-403">Изменяет директиву `page` с `@page` на `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="fa803-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="fa803-404">`"{id:int?}"` является шаблоном маршрута.</span><span class="sxs-lookup"><span data-stu-id="fa803-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="fa803-405">Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="fa803-406">Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:</span><span class="sxs-lookup"><span data-stu-id="fa803-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="fa803-407">Когда используется директива страницы `@page "{id:int?}"`, предыдущий URL-адрес имеет следующее значение:</span><span class="sxs-lookup"><span data-stu-id="fa803-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="fa803-408">Заголовком страницы является **Instructors**.</span><span class="sxs-lookup"><span data-stu-id="fa803-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="fa803-409">Добавили столбец **Office**, отображающий `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно null.</span><span class="sxs-lookup"><span data-stu-id="fa803-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="fa803-410">Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.</span><span class="sxs-lookup"><span data-stu-id="fa803-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="fa803-411">Добавили столбец **Courses**, отображающий курсы, которые ведет конкретный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="fa803-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="fa803-412">Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="fa803-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="fa803-413">Добавили код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="fa803-414">Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="fa803-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="fa803-415">Добавлена новая гиперссылка с меткой **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="fa803-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="fa803-416">Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.</span><span class="sxs-lookup"><span data-stu-id="fa803-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="fa803-417">Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="fa803-418">Если OfficeAssignment имеет значение null, отображается пустая ячейка таблицы.</span><span class="sxs-lookup"><span data-stu-id="fa803-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="fa803-419">Щелкните ссылку **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="fa803-419">Click on the **Select** link.</span></span> <span data-ttu-id="fa803-420">Изменяется стиль строки.</span><span class="sxs-lookup"><span data-stu-id="fa803-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="fa803-421">Добавление курсов, проводимых выбранным преподавателем</span><span class="sxs-lookup"><span data-stu-id="fa803-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="fa803-422">Измените метод `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="fa803-423">Добавление `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="fa803-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="fa803-424">Проверьте измененный запрос:</span><span class="sxs-lookup"><span data-stu-id="fa803-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="fa803-425">Предыдущий запрос добавляет сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="fa803-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="fa803-426">Приведенный ниже код выполняется при выборе преподавателя (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="fa803-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="fa803-427">Выбранный преподаватель извлекается из списка преподавателей в модели представления.</span><span class="sxs-lookup"><span data-stu-id="fa803-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="fa803-428">Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="fa803-429">Метод `Where` возвращает коллекцию.</span><span class="sxs-lookup"><span data-stu-id="fa803-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="fa803-430">В предыдущем методе `Where` возвращается всего одна сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="fa803-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="fa803-431">Метод `Single` преобразует коллекцию в отдельную сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="fa803-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="fa803-432">Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="fa803-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="fa803-433">`CourseAssignments` предоставляет доступ к связанным сущностям `Course`.</span><span class="sxs-lookup"><span data-stu-id="fa803-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="fa803-435">Метод `Single` используется для коллекции, когда она содержит всего один элемент.</span><span class="sxs-lookup"><span data-stu-id="fa803-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="fa803-436">Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента.</span><span class="sxs-lookup"><span data-stu-id="fa803-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="fa803-437">Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.</span><span class="sxs-lookup"><span data-stu-id="fa803-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="fa803-438">Использование `SingleOrDefault` для пустой коллекции:</span><span class="sxs-lookup"><span data-stu-id="fa803-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="fa803-439">Возникает исключение (из-за попытки найти свойство `Courses` по пустой ссылке).</span><span class="sxs-lookup"><span data-stu-id="fa803-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="fa803-440">Сообщение об исключении не так четко указывает на причину проблемы.</span><span class="sxs-lookup"><span data-stu-id="fa803-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="fa803-441">Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:</span><span class="sxs-lookup"><span data-stu-id="fa803-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="fa803-442">Добавьте следующую разметку в конец страницы Razor *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fa803-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="fa803-443">Предыдущая разметка отображает список связанных с преподавателем курсов при выборе преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="fa803-444">Тестирование приложения.</span><span class="sxs-lookup"><span data-stu-id="fa803-444">Test the app.</span></span> <span data-ttu-id="fa803-445">Щелкните ссылку **Select** (Выбрать) на странице преподавателей.</span><span class="sxs-lookup"><span data-stu-id="fa803-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="fa803-446">Отображение данных об учащихся</span><span class="sxs-lookup"><span data-stu-id="fa803-446">Show student data</span></span>

<span data-ttu-id="fa803-447">В этом разделе приложение изменяется, чтобы отобразить данные об учащихся для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="fa803-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="fa803-448">Измените запрос в методе `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="fa803-449">Измените *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fa803-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="fa803-450">Добавьте следующую разметку в конец файла:</span><span class="sxs-lookup"><span data-stu-id="fa803-450">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="fa803-451">Предыдущая разметка отображает список учащихся, которые зачислены на курс.</span><span class="sxs-lookup"><span data-stu-id="fa803-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="fa803-452">Обновите страницу и выберите преподавателя.</span><span class="sxs-lookup"><span data-stu-id="fa803-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="fa803-453">Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.</span><span class="sxs-lookup"><span data-stu-id="fa803-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="fa803-455">Использование метода Single</span><span class="sxs-lookup"><span data-stu-id="fa803-455">Using Single</span></span>

<span data-ttu-id="fa803-456">Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:</span><span class="sxs-lookup"><span data-stu-id="fa803-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="fa803-457">Приведенный выше подход на основе `Single` не дает преимуществ по сравнению с использованием `Where`.</span><span class="sxs-lookup"><span data-stu-id="fa803-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="fa803-458">Некоторые разработчики предпочитают использовать подход `Single`.</span><span class="sxs-lookup"><span data-stu-id="fa803-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="fa803-459">Явная загрузка</span><span class="sxs-lookup"><span data-stu-id="fa803-459">Explicit loading</span></span>

<span data-ttu-id="fa803-460">Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:</span><span class="sxs-lookup"><span data-stu-id="fa803-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="fa803-461">Предположим, что пользователям редко требуется просматривать зачисления на курс.</span><span class="sxs-lookup"><span data-stu-id="fa803-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="fa803-462">В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе.</span><span class="sxs-lookup"><span data-stu-id="fa803-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="fa803-463">В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.</span><span class="sxs-lookup"><span data-stu-id="fa803-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="fa803-464">Измените `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="fa803-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="fa803-465">Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся.</span><span class="sxs-lookup"><span data-stu-id="fa803-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="fa803-466">Если курс выбран, выделенный код извлекает следующее:</span><span class="sxs-lookup"><span data-stu-id="fa803-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="fa803-467">Сущности `Enrollment` для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="fa803-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="fa803-468">Сущности `Student` для каждого `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="fa803-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="fa803-469">Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="fa803-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="fa803-470">Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.</span><span class="sxs-lookup"><span data-stu-id="fa803-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="fa803-471">Тестирование приложения.</span><span class="sxs-lookup"><span data-stu-id="fa803-471">Test the app.</span></span> <span data-ttu-id="fa803-472">С точки зрения пользователей приложение работает аналогично предыдущей версии.</span><span class="sxs-lookup"><span data-stu-id="fa803-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="fa803-473">Следующее руководство посвящено обновлению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="fa803-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa803-474">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fa803-474">Additional resources</span></span>

* [<span data-ttu-id="fa803-475">Версия руководства на YouTube (часть 1)</span><span class="sxs-lookup"><span data-stu-id="fa803-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="fa803-476">Версия руководства на YouTube (часть 2)</span><span class="sxs-lookup"><span data-stu-id="fa803-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="fa803-477">[Назад](xref:data/ef-rp/complex-data-model)
>[Вперед](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="fa803-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
