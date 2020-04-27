---
title: Razor Pages с EF Core в ASP.NET Core — чтение связанных данных — 6 из 8
author: rick-anderson
description: Из этого руководства вы узнаете, как читать и отображать связанные данные — данные, которые Entity Framework загружает в свойства навигации.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 0835dbf6b5434e4224d9f56ea3335df4b5d9e119
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277331"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="ed533-103">Razor Pages с EF Core в ASP.NET Core — чтение связанных данных — 6 из 8</span><span class="sxs-lookup"><span data-stu-id="ed533-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="ed533-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="ed533-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ed533-105">Этот учебник посвящен чтению и отображению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="ed533-106">Связанными называются данные, которые EF Core загружает в свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ed533-107">На следующих рисунках изображены готовые страницы для этого руководства:</span><span class="sxs-lookup"><span data-stu-id="ed533-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="ed533-110">Безотложная, явная и отложенная загрузка</span><span class="sxs-lookup"><span data-stu-id="ed533-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="ed533-111">Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:</span><span class="sxs-lookup"><span data-stu-id="ed533-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ed533-112">[Безотложная загрузка](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ed533-113">Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="ed533-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ed533-114">При чтении сущности извлекаются ее связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ed533-115">Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ed533-116">Для некоторых типов безотложной загрузки EF Core выдает несколько запросов.</span><span class="sxs-lookup"><span data-stu-id="ed533-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ed533-117">Отправка нескольких запросов может оказаться эффективнее, чем выполнение одного большого запроса.</span><span class="sxs-lookup"><span data-stu-id="ed533-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="ed533-118">Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="ed533-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ed533-120">Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:</span><span class="sxs-lookup"><span data-stu-id="ed533-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ed533-121">Один запрос для основного запроса</span><span class="sxs-lookup"><span data-stu-id="ed533-121">One query for the main query</span></span> 
  * <span data-ttu-id="ed533-122">Один запрос для каждого "края" коллекции в дереве загрузки.</span><span class="sxs-lookup"><span data-stu-id="ed533-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ed533-123">Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ed533-124">Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ed533-125">Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.</span><span class="sxs-lookup"><span data-stu-id="ed533-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ed533-127">Примечание. EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="ed533-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ed533-128">Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="ed533-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ed533-129">[Явная загрузка](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ed533-130">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ed533-131">Нужно написать код, извлекающий связанные данные, когда они необходимы.</span><span class="sxs-lookup"><span data-stu-id="ed533-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ed533-132">Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="ed533-133">При явной загрузке код указывает, какие свойства навигации нужно загрузить.</span><span class="sxs-lookup"><span data-stu-id="ed533-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ed533-134">Для выполнения явной загрузки используется метод `Load`.</span><span class="sxs-lookup"><span data-stu-id="ed533-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ed533-135">Пример:</span><span class="sxs-lookup"><span data-stu-id="ed533-135">For example:</span></span>

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ed533-137">[Отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ed533-138">[В EF Core версии 2.1 добавлена отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ed533-139">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ed533-140">При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="ed533-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ed533-141">Запрос к базе данных отправляется при каждом первом обращении к свойству навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="ed533-142">Создание страниц курсов</span><span class="sxs-lookup"><span data-stu-id="ed533-142">Create Course pages</span></span>

<span data-ttu-id="ed533-143">Сущность `Course` включает в себя свойство навигации, содержащее связанную сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="ed533-145">Чтобы отобразить имя кафедры, которой назначен курс, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="ed533-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="ed533-146">Загрузите связанную сущность `Department` в свойство навигации `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="ed533-147">Получите имя из свойства `Name` сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="ed533-148">Формирование шаблона для страниц курсов</span><span class="sxs-lookup"><span data-stu-id="ed533-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ed533-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed533-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ed533-150">Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ed533-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ed533-151">Создайте папку *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="ed533-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="ed533-152">Используйте класс модели `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="ed533-153">Используйте существующий класс контекста вместо создания нового.</span><span class="sxs-lookup"><span data-stu-id="ed533-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ed533-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ed533-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ed533-155">Создайте папку *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="ed533-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="ed533-156">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц курсов.</span><span class="sxs-lookup"><span data-stu-id="ed533-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="ed533-157">**В Windows:**</span><span class="sxs-lookup"><span data-stu-id="ed533-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="ed533-158">**В Linux или macOS:**</span><span class="sxs-lookup"><span data-stu-id="ed533-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="ed533-159">Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="ed533-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ed533-160">Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ed533-161">Метод `Include` задает безотложную загрузку.</span><span class="sxs-lookup"><span data-stu-id="ed533-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="ed533-162">Запустите приложение и выберите ссылку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="ed533-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ed533-163">В столбце кафедры отображается бесполезный `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="ed533-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="ed533-164">Отображение названия кафедры</span><span class="sxs-lookup"><span data-stu-id="ed533-164">Display the department name</span></span>

<span data-ttu-id="ed533-165">Измените файл Pages/Courses/Index.cshtml.cs, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="ed533-166">Приведенный выше код изменяет свойство `Course` на `Courses` и добавляет `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="ed533-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="ed533-167">`AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ed533-168">Отслеживать сущности не нужно, так как они не изменяются в текущем контексте.</span><span class="sxs-lookup"><span data-stu-id="ed533-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ed533-169">Измените файл *Pages/Courses/Index.cshtml*, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ed533-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="ed533-170">В шаблонный код были внесены следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ed533-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ed533-171">Имя свойства `Course` изменилось на `Courses`.</span><span class="sxs-lookup"><span data-stu-id="ed533-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="ed533-172">Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ed533-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ed533-173">По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла.</span><span class="sxs-lookup"><span data-stu-id="ed533-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ed533-174">Однако в нашем случае первичный ключ имеет смысл.</span><span class="sxs-lookup"><span data-stu-id="ed533-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ed533-175">Изменен столбец **Department** (Кафедра) для отображения названия кафедры.</span><span class="sxs-lookup"><span data-stu-id="ed533-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ed533-176">Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:</span><span class="sxs-lookup"><span data-stu-id="ed533-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ed533-177">Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="ed533-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ed533-179">Загрузка связанных данных с помощью "Select"</span><span class="sxs-lookup"><span data-stu-id="ed533-179">Loading related data with Select</span></span>

<span data-ttu-id="ed533-180">Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`.</span><span class="sxs-lookup"><span data-stu-id="ed533-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="ed533-181">Метод `Select` является альтернативным вариантом, который загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="ed533-182">Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="ed533-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ed533-183">Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.</span><span class="sxs-lookup"><span data-stu-id="ed533-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ed533-184">Следующий код загружает связанные данные с помощью метода `Select`:</span><span class="sxs-lookup"><span data-stu-id="ed533-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="ed533-185">Приведенный выше код не возвращает никаких типов сущностей, поэтому отслеживание не выполняется.</span><span class="sxs-lookup"><span data-stu-id="ed533-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="ed533-186">Дополнительные сведения об отслеживании EF см. в разделе [Отслеживание или Отключение отслеживания запросов](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="ed533-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="ed533-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="ed533-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ed533-188">Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="ed533-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="ed533-189">Создание страниц преподавателей</span><span class="sxs-lookup"><span data-stu-id="ed533-189">Create Instructor pages</span></span>

<span data-ttu-id="ed533-190">В этом разделе формируется шаблон для страниц преподавателей, а затем на страницу указателя преподавателей добавляются связанные курсы и регистрации.</span><span class="sxs-lookup"><span data-stu-id="ed533-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="ed533-191">![Страница индекса преподавателей](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="ed533-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="ed533-192">Эта страница считывает и отображает связанные данные следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ed533-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ed533-193">Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении).</span><span class="sxs-lookup"><span data-stu-id="ed533-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ed533-194">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="ed533-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ed533-195">Безотложная загрузка используется для сущностей `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ed533-196">Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ed533-197">В этом случае отображается принадлежность к кабинету для каждого преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ed533-198">Когда пользователь выбирает преподавателя, отображаются связанные сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="ed533-199">Между сущностями `Instructor` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="ed533-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ed533-200">Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ed533-201">В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ed533-202">Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ed533-203">Когда пользователь выбирает курс, отображаются связанные данные из сущности `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="ed533-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ed533-204">На предыдущем изображении отображается имя и оценка учащегося.</span><span class="sxs-lookup"><span data-stu-id="ed533-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ed533-205">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="ed533-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="ed533-206">Создание модели представления</span><span class="sxs-lookup"><span data-stu-id="ed533-206">Create a view model</span></span>

<span data-ttu-id="ed533-207">На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц.</span><span class="sxs-lookup"><span data-stu-id="ed533-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ed533-208">Требуется модель представления, которая включает в себя три свойства, представляющие три таблицы.</span><span class="sxs-lookup"><span data-stu-id="ed533-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="ed533-209">Создайте файл *SchoolViewModels/InstructorIndexData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ed533-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="ed533-210">Формирование шаблона для страниц преподавателей</span><span class="sxs-lookup"><span data-stu-id="ed533-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ed533-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed533-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ed533-212">Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="ed533-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ed533-213">Создайте папку *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="ed533-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="ed533-214">Используйте класс модели `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="ed533-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="ed533-215">Используйте существующий класс контекста вместо создания нового.</span><span class="sxs-lookup"><span data-stu-id="ed533-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ed533-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ed533-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ed533-217">Создайте папку *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="ed533-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="ed533-218">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц преподавателей.</span><span class="sxs-lookup"><span data-stu-id="ed533-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="ed533-219">**В Windows:**</span><span class="sxs-lookup"><span data-stu-id="ed533-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="ed533-220">**В Linux или macOS:**</span><span class="sxs-lookup"><span data-stu-id="ed533-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ed533-221">Чтобы увидеть, как выглядит шаблонная страница до ее изменения, запустите приложение и перейдите на страницу преподавателей.</span><span class="sxs-lookup"><span data-stu-id="ed533-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="ed533-222">Измените файл *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="ed533-223">Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ed533-224">Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ed533-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="ed533-225">В коде задается безотложная загрузка для следующих свойств навигации:</span><span class="sxs-lookup"><span data-stu-id="ed533-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="ed533-226">Обратите внимание на то, что методы `Include` и `ThenInclude` повторяются для `CourseAssignments` и `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="ed533-227">Это необходимо для того, чтобы задать безотложную загрузку для двух свойств навигации сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="ed533-228">Приведенный ниже код выполняется при выборе преподавателя (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="ed533-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="ed533-229">Выбранный преподаватель извлекается из списка преподавателей в модели представления.</span><span class="sxs-lookup"><span data-stu-id="ed533-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ed533-230">Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="ed533-231">Метод `Where` возвращает коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ed533-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="ed533-232">Однако в этом случае фильтр выберет одну сущность.</span><span class="sxs-lookup"><span data-stu-id="ed533-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="ed533-233">Поэтому вызывается метод `Single` для преобразования коллекции в отдельную сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="ed533-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ed533-234">Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ed533-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ed533-235">`CourseAssignments` предоставляет доступ к связанным сущностям `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ed533-237">Метод `Single` используется для коллекции, когда она содержит всего один элемент.</span><span class="sxs-lookup"><span data-stu-id="ed533-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ed533-238">Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента.</span><span class="sxs-lookup"><span data-stu-id="ed533-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ed533-239">Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.</span><span class="sxs-lookup"><span data-stu-id="ed533-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="ed533-240">Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:</span><span class="sxs-lookup"><span data-stu-id="ed533-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ed533-241">Изменение страницы индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="ed533-241">Update the instructors Index page</span></span>

<span data-ttu-id="ed533-242">Измените файл *Pages/Instructors/Index.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="ed533-243">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ed533-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ed533-244">Изменяет директиву `page` с `@page` на `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="ed533-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ed533-245">`"{id:int?}"` является шаблоном маршрута.</span><span class="sxs-lookup"><span data-stu-id="ed533-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ed533-246">Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ed533-247">Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:</span><span class="sxs-lookup"><span data-stu-id="ed533-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="ed533-248">Когда используется директива страницы `@page "{id:int?}"`, URL-адрес имеет следующее значение:</span><span class="sxs-lookup"><span data-stu-id="ed533-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="ed533-249">Добавляет столбец **Office**, в котором отображается `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно NULL.</span><span class="sxs-lookup"><span data-stu-id="ed533-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ed533-250">Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.</span><span class="sxs-lookup"><span data-stu-id="ed533-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ed533-251">Добавляет столбец **Courses**, содержащий курсы, которые ведет конкретный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="ed533-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ed533-252">Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="ed533-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="ed533-253">Добавляет код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя и курса.</span><span class="sxs-lookup"><span data-stu-id="ed533-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="ed533-254">Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="ed533-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ed533-255">Добавляет новую гиперссылку с меткой **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="ed533-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ed533-256">Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.</span><span class="sxs-lookup"><span data-stu-id="ed533-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="ed533-257">Добавляет таблицу курсов для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="ed533-258">Добавляет таблицу регистраций учащихся для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="ed533-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="ed533-259">Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ed533-260">Если `OfficeAssignment` имеет значение NULL, отображается пустая ячейка таблицы.</span><span class="sxs-lookup"><span data-stu-id="ed533-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ed533-261">Щелкните ссылку **Select** для преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="ed533-262">Стиль строки изменится, и отобразятся курсы, назначенные этому преподавателю.</span><span class="sxs-lookup"><span data-stu-id="ed533-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="ed533-263">Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.</span><span class="sxs-lookup"><span data-stu-id="ed533-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="ed533-265">Использование метода Single</span><span class="sxs-lookup"><span data-stu-id="ed533-265">Using Single</span></span>

<span data-ttu-id="ed533-266">Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:</span><span class="sxs-lookup"><span data-stu-id="ed533-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ed533-267">Использование `Single` с условием WHERE — это вопрос личных предпочтений.</span><span class="sxs-lookup"><span data-stu-id="ed533-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="ed533-268">Оно не дает преимуществ по сравнению с использованием метода `Where`.</span><span class="sxs-lookup"><span data-stu-id="ed533-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ed533-269">Явная загрузка</span><span class="sxs-lookup"><span data-stu-id="ed533-269">Explicit loading</span></span>

<span data-ttu-id="ed533-270">Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:</span><span class="sxs-lookup"><span data-stu-id="ed533-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="ed533-271">Предположим, что пользователям редко требуется просматривать зачисления на курс.</span><span class="sxs-lookup"><span data-stu-id="ed533-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ed533-272">В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе.</span><span class="sxs-lookup"><span data-stu-id="ed533-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ed533-273">В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.</span><span class="sxs-lookup"><span data-stu-id="ed533-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ed533-274">Измените файл *Pages/Instructors/Index.cshtml.cs*, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ed533-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="ed533-275">Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся.</span><span class="sxs-lookup"><span data-stu-id="ed533-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ed533-276">Если курс выбран, код явной загрузки извлекает следующее:</span><span class="sxs-lookup"><span data-stu-id="ed533-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="ed533-277">Сущности `Enrollment` для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="ed533-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ed533-278">Сущности `Student` для каждого `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ed533-279">Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="ed533-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ed533-280">Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.</span><span class="sxs-lookup"><span data-stu-id="ed533-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ed533-281">Проверьте работу приложения.</span><span class="sxs-lookup"><span data-stu-id="ed533-281">Test the app.</span></span> <span data-ttu-id="ed533-282">С точки зрения пользователя приложение работает аналогично предыдущей версии.</span><span class="sxs-lookup"><span data-stu-id="ed533-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ed533-283">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ed533-283">Next steps</span></span>

<span data-ttu-id="ed533-284">Следующее руководство посвящено обновлению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="ed533-285">[Предыдущий учебник](xref:data/ef-rp/complex-data-model)
>[Следующий учебник](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ed533-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ed533-286">В этом руководстве выполняется чтение и отображение связанных данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="ed533-287">Связанными называются данные, которые EF Core загружает в свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ed533-288">При возникновении проблем, которые вам не удается устранить, [скачайте или просмотрите готовое приложение.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="ed533-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="ed533-289">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ed533-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ed533-290">На следующих рисунках изображены готовые страницы для этого руководства:</span><span class="sxs-lookup"><span data-stu-id="ed533-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="ed533-293">Безотложная, явная и отложенная загрузка связанных данных</span><span class="sxs-lookup"><span data-stu-id="ed533-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="ed533-294">Существует несколько способов, которыми EF Core может загружать связанные данные в свойства навигации сущности:</span><span class="sxs-lookup"><span data-stu-id="ed533-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ed533-295">[Безотложная загрузка](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ed533-296">Безотложной является загрузка, когда запрос для одного типа сущности также загружает связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="ed533-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ed533-297">При чтении сущности связанные данные извлекаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ed533-298">Обычно такая загрузка представляет собой одиночный запрос с соединением, который получает все необходимые данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ed533-299">Для некоторых типов безотложной загрузки EF Core выдает несколько запросов.</span><span class="sxs-lookup"><span data-stu-id="ed533-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ed533-300">Выдача нескольких запросов может оказаться более эффективной по сравнению с отдельными ситуациями в EF6, когда выполнялся отдельный запрос.</span><span class="sxs-lookup"><span data-stu-id="ed533-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="ed533-301">Безотложная загрузка указывается с помощью методов `Include` и `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="ed533-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Пример безотложной загрузки](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ed533-303">Безотложная загрузка отправляет несколько запросов, когда включена навигация коллекции:</span><span class="sxs-lookup"><span data-stu-id="ed533-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ed533-304">Один запрос для основного запроса</span><span class="sxs-lookup"><span data-stu-id="ed533-304">One query for the main query</span></span> 
  * <span data-ttu-id="ed533-305">Один запрос для каждого "края" коллекции в дереве загрузки.</span><span class="sxs-lookup"><span data-stu-id="ed533-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ed533-306">Отдельные запросы с `Load`: данные можно получить в отдельных запросах, а EF Core исправляет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ed533-307">Термин "исправляет" означает, что EF Core автоматически заполняет свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ed533-308">Отдельные запросы с `Load` больше похожи на явную загрузку, чем на безотложную.</span><span class="sxs-lookup"><span data-stu-id="ed533-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Пример отдельных запросов](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ed533-310">Примечание. EF Core автоматически исправляет свойства навигации для других сущностей, которые были ранее загружены в экземпляр контекста.</span><span class="sxs-lookup"><span data-stu-id="ed533-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ed533-311">Даже если данные для свойства навигации *не* включены явно, свойство все равно можно заполнить при условии, что ранее были загружены некоторые или все связанные сущности.</span><span class="sxs-lookup"><span data-stu-id="ed533-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ed533-312">[Явная загрузка](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ed533-313">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ed533-314">Нужно написать код, извлекающий связанные данные, когда они необходимы.</span><span class="sxs-lookup"><span data-stu-id="ed533-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ed533-315">Явная загрузка с отдельными запросами приводит к отправке нескольких запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="ed533-316">При явной загрузке код указывает, какие свойства навигации нужно загрузить.</span><span class="sxs-lookup"><span data-stu-id="ed533-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ed533-317">Для выполнения явной загрузки используется метод `Load`.</span><span class="sxs-lookup"><span data-stu-id="ed533-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ed533-318">Пример:</span><span class="sxs-lookup"><span data-stu-id="ed533-318">For example:</span></span>

  ![Пример явной загрузки](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ed533-320">[Отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ed533-321">[В EF Core версии 2.1 добавлена отложенная загрузка](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ed533-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ed533-322">При первом чтении сущности связанные данные не извлекаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ed533-323">При первом обращении к свойству навигации необходимые для этого свойства данные извлекаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="ed533-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ed533-324">Запрос к базе данных отправляется при каждом первом обращении к свойству навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="ed533-325">Оператор `Select` загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="ed533-326">Создание страницы "Course" (Курс) с отображением названий кафедр</span><span class="sxs-lookup"><span data-stu-id="ed533-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="ed533-327">Сущность "Course" включает в себя свойство навигации, содержащее сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="ed533-328">Сущность `Department` содержит кафедру, которой назначен курс.</span><span class="sxs-lookup"><span data-stu-id="ed533-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="ed533-329">Отображение имени назначенной кафедры в списке курсов:</span><span class="sxs-lookup"><span data-stu-id="ed533-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="ed533-330">Получите свойство `Name` из сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="ed533-331">Сущность `Department` получается из свойства навигации `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="ed533-333">Формирование шаблона для модели Course</span><span class="sxs-lookup"><span data-stu-id="ed533-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ed533-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed533-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ed533-335">Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Course` для класса модели.</span><span class="sxs-lookup"><span data-stu-id="ed533-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ed533-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ed533-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ed533-337">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ed533-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="ed533-338">Предыдущая команда формирует шаблон для модели `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="ed533-339">Откройте проект в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ed533-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="ed533-340">Откройте *Pages/Courses/Index.cshtml.cs* и изучите метод `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="ed533-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ed533-341">Подсистема формирования шаблонов указала безотложную загрузку для свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ed533-342">Метод `Include` задает безотложную загрузку.</span><span class="sxs-lookup"><span data-stu-id="ed533-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="ed533-343">Запустите приложение и выберите ссылку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="ed533-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ed533-344">В столбце кафедры отображается бесполезный `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="ed533-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="ed533-345">Обновите метод `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="ed533-346">Предыдущий код добавляет `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="ed533-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="ed533-347">`AsNoTracking` повышает производительность, так как возвращаемые сущности не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="ed533-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ed533-348">Отслеживание отсутствует, так как сущности не изменяются в текущем контексте.</span><span class="sxs-lookup"><span data-stu-id="ed533-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ed533-349">Измените *Pages/Courses/Index.cshtml*, используя выделенную ниже разметку:</span><span class="sxs-lookup"><span data-stu-id="ed533-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="ed533-350">В шаблонный код были внесены следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ed533-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ed533-351">Изменен заголовок с "Index" (Индекс) на "Courses" (Курсы).</span><span class="sxs-lookup"><span data-stu-id="ed533-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="ed533-352">Добавлен столбец **Number** (Номер), отображающий значение свойства `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ed533-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ed533-353">По умолчанию в шаблоне отсутствуют первичные ключи, поскольку для конечных пользователей они не имеют смысла.</span><span class="sxs-lookup"><span data-stu-id="ed533-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ed533-354">Однако в нашем случае первичный ключ имеет смысл.</span><span class="sxs-lookup"><span data-stu-id="ed533-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ed533-355">Изменен столбец **Department** (Кафедра) для отображения названия кафедры.</span><span class="sxs-lookup"><span data-stu-id="ed533-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ed533-356">Код отображает свойство `Name` сущности `Department`, которая загружена в свойство навигации `Department`:</span><span class="sxs-lookup"><span data-stu-id="ed533-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ed533-357">Для просмотра списка с названиями кафедр запустите приложение и выберите вкладку **Courses** (Курсы).</span><span class="sxs-lookup"><span data-stu-id="ed533-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Страница индекса курсов](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ed533-359">Загрузка связанных данных с помощью "Select"</span><span class="sxs-lookup"><span data-stu-id="ed533-359">Loading related data with Select</span></span>

<span data-ttu-id="ed533-360">Метод `OnGetAsync` загружает связанные данные с помощью метода `Include`:</span><span class="sxs-lookup"><span data-stu-id="ed533-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ed533-361">Оператор `Select` загружает только необходимые связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="ed533-362">Для отдельных элементов, таких как `Department.Name`, используется SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="ed533-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ed533-363">Для коллекций используется доступ к другой базе данных, но это же делает и оператор `Include` в коллекциях.</span><span class="sxs-lookup"><span data-stu-id="ed533-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ed533-364">Следующий код загружает связанные данные с помощью метода `Select`:</span><span class="sxs-lookup"><span data-stu-id="ed533-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ed533-365">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="ed533-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ed533-366">Полный пример см. в описании [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) и [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="ed533-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="ed533-367">Создание страницы "Instructors" (Преподаватели) с отображением курсов и дат зачисления</span><span class="sxs-lookup"><span data-stu-id="ed533-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="ed533-368">В этом разделе создается страница "Instructors" (Преподаватели).</span><span class="sxs-lookup"><span data-stu-id="ed533-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="ed533-369">![Страница индекса преподавателей](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="ed533-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="ed533-370">Эта страница считывает и отображает связанные данные следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ed533-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ed533-371">Список преподавателей отображает связанные данные из сущности `OfficeAssignment` ("Office" (Кабинет) на предыдущем изображении).</span><span class="sxs-lookup"><span data-stu-id="ed533-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ed533-372">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="ed533-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ed533-373">Безотложная загрузка используется для сущностей `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ed533-374">Безотложная загрузка обычно более эффективна, когда требуется отобразить связанные данные.</span><span class="sxs-lookup"><span data-stu-id="ed533-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ed533-375">В этом случае отображается принадлежность к кабинету для каждого преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ed533-376">Когда пользователь выбирает преподавателя (Harui на предыдущем изображении), отображаются связанные сущности `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="ed533-377">Между сущностями `Instructor` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="ed533-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ed533-378">Используется безотложная загрузка для сущностей `Course` и связанных сущностей `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ed533-379">В этом случае отдельные запросы могут оказаться эффективнее, так как требуются только курсы для выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ed533-380">Этот пример показывает, как использовать безотложную загрузку для свойств навигации в сущностях, находящихся в свойствах навигации.</span><span class="sxs-lookup"><span data-stu-id="ed533-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ed533-381">Когда пользователь выбирает курс ("Chemistry" (Химия) на предыдущем изображении), отображаются связанные данные из сущности `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="ed533-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ed533-382">На предыдущем изображении отображается имя и оценка учащегося.</span><span class="sxs-lookup"><span data-stu-id="ed533-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ed533-383">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="ed533-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="ed533-384">Создание модели для представления индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="ed533-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="ed533-385">На странице "Instructors" (Преподаватели) отображаются данные из трех различных таблиц.</span><span class="sxs-lookup"><span data-stu-id="ed533-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ed533-386">Создается модель представления, которая включает три сущности, представляющие три таблицы.</span><span class="sxs-lookup"><span data-stu-id="ed533-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="ed533-387">Создайте в папке *SchoolViewModels* файл *InstructorIndexData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ed533-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="ed533-388">Формирование шаблона для модели "Instructor" (Преподаватель)</span><span class="sxs-lookup"><span data-stu-id="ed533-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ed533-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed533-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ed533-390">Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-the-student-model) и используйте `Instructor` для класса модели.</span><span class="sxs-lookup"><span data-stu-id="ed533-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ed533-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ed533-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ed533-392">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ed533-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ed533-393">Предыдущая команда формирует шаблон для модели `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="ed533-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="ed533-394">Запустите приложение и перейдите на страницу преподавателей.</span><span class="sxs-lookup"><span data-stu-id="ed533-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="ed533-395">Замените содержимое *Pages/Instructors/Index.cshtml.cs* на следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="ed533-396">Метод `OnGetAsync` принимает необязательные данные маршрутизации для идентификатора выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ed533-397">Изучите запрос в файле *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ed533-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ed533-398">Запрос имеет две операции включения:</span><span class="sxs-lookup"><span data-stu-id="ed533-398">The query has two includes:</span></span>

* <span data-ttu-id="ed533-399">`OfficeAssignment`. отображается в [представлении преподавателей](#IP).</span><span class="sxs-lookup"><span data-stu-id="ed533-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="ed533-400">`CourseAssignments`. вызывает проводимые курсы.</span><span class="sxs-lookup"><span data-stu-id="ed533-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ed533-401">Изменение страницы индекса преподавателей</span><span class="sxs-lookup"><span data-stu-id="ed533-401">Update the instructors Index page</span></span>

<span data-ttu-id="ed533-402">Измените *Pages/Instructors/Index.cshtml*, используя следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="ed533-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="ed533-403">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="ed533-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ed533-404">Изменяет директиву `page` с `@page` на `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="ed533-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ed533-405">`"{id:int?}"` является шаблоном маршрута.</span><span class="sxs-lookup"><span data-stu-id="ed533-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ed533-406">Шаблон маршрута изменяет целочисленные строки запроса в URL-адресе для маршрутизации данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ed533-407">Например, при выборе ссылки **Select** для преподавателя только с директивой `@page` формируется URL-адрес следующего вида:</span><span class="sxs-lookup"><span data-stu-id="ed533-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="ed533-408">Когда используется директива страницы `@page "{id:int?}"`, предыдущий URL-адрес имеет следующее значение:</span><span class="sxs-lookup"><span data-stu-id="ed533-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="ed533-409">Заголовком страницы является **Instructors**.</span><span class="sxs-lookup"><span data-stu-id="ed533-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="ed533-410">Добавили столбец **Office**, отображающий `item.OfficeAssignment.Location` только тогда, когда `item.OfficeAssignment` не равно null.</span><span class="sxs-lookup"><span data-stu-id="ed533-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ed533-411">Так как это связь один к нулю или одному, то связанная сущность OfficeAssignment может отсутствовать.</span><span class="sxs-lookup"><span data-stu-id="ed533-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ed533-412">Добавили столбец **Courses**, отображающий курсы, которые ведет конкретный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="ed533-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ed533-413">Подробные сведения об использовании синтаксиса Razor см. в разделе [Явный перенос строки](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="ed533-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="ed533-414">Добавили код, который динамически добавляет `class="success"` к элементу `tr` выбранного преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="ed533-415">Этот параметр задает цвет фона для выделенных строк c помощью класса Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="ed533-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ed533-416">Добавлена новая гиперссылка с меткой **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="ed533-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ed533-417">Она отправляет идентификатор выбранного преподавателя в метод `Index` и задает цвет фона.</span><span class="sxs-lookup"><span data-stu-id="ed533-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="ed533-418">Запустите приложение и выберите вкладку **Instructors**. На странице отображается `Location` (кабинет) из связанной сущности `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ed533-419">Если OfficeAssignment имеет значение null, отображается пустая ячейка таблицы.</span><span class="sxs-lookup"><span data-stu-id="ed533-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ed533-420">Щелкните ссылку **Select** (Выбрать).</span><span class="sxs-lookup"><span data-stu-id="ed533-420">Click on the **Select** link.</span></span> <span data-ttu-id="ed533-421">Изменяется стиль строки.</span><span class="sxs-lookup"><span data-stu-id="ed533-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="ed533-422">Добавление курсов, проводимых выбранным преподавателем</span><span class="sxs-lookup"><span data-stu-id="ed533-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="ed533-423">Измените метод `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="ed533-424">Добавление `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="ed533-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="ed533-425">Проверьте измененный запрос:</span><span class="sxs-lookup"><span data-stu-id="ed533-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ed533-426">Предыдущий запрос добавляет сущности `Department`.</span><span class="sxs-lookup"><span data-stu-id="ed533-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="ed533-427">Приведенный ниже код выполняется при выборе преподавателя (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="ed533-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="ed533-428">Выбранный преподаватель извлекается из списка преподавателей в модели представления.</span><span class="sxs-lookup"><span data-stu-id="ed533-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ed533-429">Из свойства навигации `CourseAssignments` этого преподавателя загружается свойство модели представления `Courses` вместе с сущностями `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="ed533-430">Метод `Where` возвращает коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ed533-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="ed533-431">В предыдущем методе `Where` возвращается всего одна сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="ed533-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="ed533-432">Метод `Single` преобразует коллекцию в отдельную сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="ed533-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ed533-433">Сущность `Instructor` предоставляет доступ к свойству `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ed533-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ed533-434">`CourseAssignments` предоставляет доступ к связанным сущностям `Course`.</span><span class="sxs-lookup"><span data-stu-id="ed533-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ed533-436">Метод `Single` используется для коллекции, когда она содержит всего один элемент.</span><span class="sxs-lookup"><span data-stu-id="ed533-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ed533-437">Метод `Single` вызывает исключение, если коллекция пуста или содержит больше одного элемента.</span><span class="sxs-lookup"><span data-stu-id="ed533-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ed533-438">Альтернативным вариантом является метод `SingleOrDefault`, который возвращает значение по умолчанию (в данном случае null), если коллекция пуста.</span><span class="sxs-lookup"><span data-stu-id="ed533-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="ed533-439">Использование `SingleOrDefault` для пустой коллекции:</span><span class="sxs-lookup"><span data-stu-id="ed533-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="ed533-440">Возникает исключение (из-за попытки найти свойство `Courses` по пустой ссылке).</span><span class="sxs-lookup"><span data-stu-id="ed533-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="ed533-441">Сообщение об исключении не так четко указывает на причину проблемы.</span><span class="sxs-lookup"><span data-stu-id="ed533-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="ed533-442">Следующий код заполняет свойство `Enrollments` модели представления при выборе курса:</span><span class="sxs-lookup"><span data-stu-id="ed533-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="ed533-443">Добавьте следующую разметку в конец страницы Razor *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ed533-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="ed533-444">Предыдущая разметка отображает список связанных с преподавателем курсов при выборе преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="ed533-445">Проверьте работу приложения.</span><span class="sxs-lookup"><span data-stu-id="ed533-445">Test the app.</span></span> <span data-ttu-id="ed533-446">Щелкните ссылку **Select** (Выбрать) на странице преподавателей.</span><span class="sxs-lookup"><span data-stu-id="ed533-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="ed533-447">Отображение данных об учащихся</span><span class="sxs-lookup"><span data-stu-id="ed533-447">Show student data</span></span>

<span data-ttu-id="ed533-448">В этом разделе приложение изменяется, чтобы отобразить данные об учащихся для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="ed533-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="ed533-449">Измените запрос в методе `OnGetAsync` в *Pages/Instructors/Index.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ed533-450">Измените *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ed533-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="ed533-451">Добавьте следующую разметку в конец файла:</span><span class="sxs-lookup"><span data-stu-id="ed533-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="ed533-452">Предыдущая разметка отображает список учащихся, которые зачислены на курс.</span><span class="sxs-lookup"><span data-stu-id="ed533-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="ed533-453">Обновите страницу и выберите преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ed533-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="ed533-454">Выберите курс, чтобы увидеть список зачисленных учащихся и их оценки.</span><span class="sxs-lookup"><span data-stu-id="ed533-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Страница индекса преподавателей, выбраны преподаватель и курс](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="ed533-456">Использование метода Single</span><span class="sxs-lookup"><span data-stu-id="ed533-456">Using Single</span></span>

<span data-ttu-id="ed533-457">Метод `Single` может передать условие `Where` вместо отдельного вызова метода `Where`:</span><span class="sxs-lookup"><span data-stu-id="ed533-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ed533-458">Приведенный выше подход на основе `Single` не дает преимуществ по сравнению с использованием `Where`.</span><span class="sxs-lookup"><span data-stu-id="ed533-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="ed533-459">Некоторые разработчики предпочитают использовать подход `Single`.</span><span class="sxs-lookup"><span data-stu-id="ed533-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ed533-460">Явная загрузка</span><span class="sxs-lookup"><span data-stu-id="ed533-460">Explicit loading</span></span>

<span data-ttu-id="ed533-461">Текущий код указывает упреждающую загрузку для `Enrollments` и `Students`:</span><span class="sxs-lookup"><span data-stu-id="ed533-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ed533-462">Предположим, что пользователям редко требуется просматривать зачисления на курс.</span><span class="sxs-lookup"><span data-stu-id="ed533-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ed533-463">В этом случае можно оптимизировать работу, загружая данные о зачислении только при их запросе.</span><span class="sxs-lookup"><span data-stu-id="ed533-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ed533-464">В этом разделе изменяется `OnGetAsync` для использования явной загрузки `Enrollments` и `Students`.</span><span class="sxs-lookup"><span data-stu-id="ed533-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ed533-465">Измените `OnGetAsync`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="ed533-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="ed533-466">Предыдущий код удаляет вызовы метода *ThenInclude* для данных о зачислении и учащихся.</span><span class="sxs-lookup"><span data-stu-id="ed533-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ed533-467">Если курс выбран, выделенный код извлекает следующее:</span><span class="sxs-lookup"><span data-stu-id="ed533-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="ed533-468">Сущности `Enrollment` для выбранного курса.</span><span class="sxs-lookup"><span data-stu-id="ed533-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ed533-469">Сущности `Student` для каждого `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="ed533-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ed533-470">Обратите внимание, что предыдущий код закомментирует `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="ed533-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ed533-471">Для отслеживаемых сущностей свойства навигации можно загрузить лишь явно.</span><span class="sxs-lookup"><span data-stu-id="ed533-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ed533-472">Проверьте работу приложения.</span><span class="sxs-lookup"><span data-stu-id="ed533-472">Test the app.</span></span> <span data-ttu-id="ed533-473">С точки зрения пользователей приложение работает аналогично предыдущей версии.</span><span class="sxs-lookup"><span data-stu-id="ed533-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="ed533-474">Следующее руководство посвящено обновлению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="ed533-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed533-475">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ed533-475">Additional resources</span></span>

* [<span data-ttu-id="ed533-476">Версия руководства на YouTube (часть 1)</span><span class="sxs-lookup"><span data-stu-id="ed533-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="ed533-477">Версия руководства на YouTube (часть 2)</span><span class="sxs-lookup"><span data-stu-id="ed533-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="ed533-478">[Назад](xref:data/ef-rp/complex-data-model)
>[Вперед](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ed533-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
