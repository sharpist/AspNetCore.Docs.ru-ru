---
title: Часть 7. Razor Pages с EF Core в ASP.NET Core — обновление связанных данных
author: rick-anderson
description: Часть 7 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b084404a7fdd8b2fc18c407bd11705ccd1c496c1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406307"
---
# <a name="part-7-razor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="2aec8-103">Часть 7. Razor Pages с EF Core в ASP.NET Core — обновление связанных данных</span><span class="sxs-lookup"><span data-stu-id="2aec8-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="2aec8-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2aec8-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2aec8-105">Этот учебник посвящен обновлению связанных данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="2aec8-106">На рисунках ниже показаны некоторые готовые страницы.</span><span class="sxs-lookup"><span data-stu-id="2aec8-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="2aec8-107">![Страница редактирования курса](update-related-data/_static/course-edit30.png)
![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="2aec8-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="2aec8-108">Обновление страниц создания и редактирования курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="2aec8-109">Шаблонный код для страниц создания и редактирования курсов содержит раскрывающийся список Department (Кафедра) с идентификаторами кафедр (целочисленными).</span><span class="sxs-lookup"><span data-stu-id="2aec8-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="2aec8-110">В этом раскрывающемся списке должны отображаться названия кафедр, поэтому для каждой из этих страниц требуется список названий кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="2aec8-111">Чтобы предоставить этот список, используйте базовый класс для страниц создания и редактирования.</span><span class="sxs-lookup"><span data-stu-id="2aec8-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="2aec8-112">Создание базового класса для создания и редактирования курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="2aec8-113">Создайте файл *Pages/Courses/DepartmentNamePageModel.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="2aec8-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="2aec8-114">Приведенный выше код создает [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), содержащий список названий кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="2aec8-115">Если указан параметр `selectedDepartment`, кафедра выбрана в списке `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="2aec8-116">Классы моделей страниц Create и Edit являются производными от `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="2aec8-117">Обновление модели для страницы создания курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-117">Update the Course Create page model</span></span>

<span data-ttu-id="2aec8-118">Курс назначается кафедре.</span><span class="sxs-lookup"><span data-stu-id="2aec8-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="2aec8-119">Базовый класс для страниц создания и редактирования предоставляет список `SelectList` для выбора кафедры.</span><span class="sxs-lookup"><span data-stu-id="2aec8-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="2aec8-120">Раскрывающийся список, использующий `SelectList`, устанавливает свойство внешнего ключа `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="2aec8-121">Платформа EF Core использует внешний ключ `Course.DepartmentID` для загрузки свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Создание курса](update-related-data/_static/ddl30.png)

<span data-ttu-id="2aec8-123">Измените файл *Pages/Courses/Create.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="2aec8-124">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-124">The preceding code:</span></span>

* <span data-ttu-id="2aec8-125">Происходит от `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="2aec8-126">Использует `TryUpdateModelAsync`, чтобы предотвратить [чрезмерную передачу данных](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2aec8-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2aec8-127">Удаляет `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="2aec8-128">`DepartmentNameSL` из базового класса — это строго типизированная модель, которая будет использоваться страницей Razor.</span><span class="sxs-lookup"><span data-stu-id="2aec8-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="2aec8-129">Вместо слабо типизированных моделей рекомендуется использовать строго типизированные.</span><span class="sxs-lookup"><span data-stu-id="2aec8-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="2aec8-130">Дополнительные сведения см. в разделе [Слабо типизированные данные (ViewData и ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="2aec8-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="2aec8-131">Обновление страницы Razor создания курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="2aec8-132">Измените файл *Pages/Courses/Create.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="2aec8-133">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2aec8-134">Изменяет заголовок с **DepartmentID** на **Department**.</span><span class="sxs-lookup"><span data-stu-id="2aec8-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2aec8-135">Заменяет `"ViewBag.DepartmentID"` на `DepartmentNameSL` (из базового класса).</span><span class="sxs-lookup"><span data-stu-id="2aec8-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="2aec8-136">Добавляет параметр "Select Department" (Выбор кафедры).</span><span class="sxs-lookup"><span data-stu-id="2aec8-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="2aec8-137">В результате этого изменения вместо первой кафедры в раскрывающемся списке отображается пункт "Select Department" (Выберите кафедру), если кафедра еще не выбрана.</span><span class="sxs-lookup"><span data-stu-id="2aec8-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="2aec8-138">Добавляет сообщение о проверке в том случае, если не выбрана кафедра.</span><span class="sxs-lookup"><span data-stu-id="2aec8-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="2aec8-139">На странице Razor Pages используется [вспомогательная функция тега Select](xref:mvc/views/working-with-forms#the-select-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2aec8-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="2aec8-140">Протестируйте страницу создания.</span><span class="sxs-lookup"><span data-stu-id="2aec8-140">Test the Create page.</span></span> <span data-ttu-id="2aec8-141">На странице Create отображается название, а не идентификатор кафедры.</span><span class="sxs-lookup"><span data-stu-id="2aec8-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="2aec8-142">Обновление модели для страницы редактирования курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-142">Update the Course Edit page model</span></span>

<span data-ttu-id="2aec8-143">Измените файл *Pages/Courses/Edit.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="2aec8-144">Изменения аналогичны внесенным в модель страницы Create.</span><span class="sxs-lookup"><span data-stu-id="2aec8-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="2aec8-145">В приведенном выше коде `PopulateDepartmentsDropDownList` передает идентификатор кафедры, по которому выбирается кафедра в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="2aec8-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="2aec8-146">Обновление страницы Razor редактирования курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="2aec8-147">Измените файл *Pages/Courses/Edit.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="2aec8-148">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2aec8-149">Отображает идентификатор курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-149">Displays the course ID.</span></span> <span data-ttu-id="2aec8-150">Как правило, первичный ключ сущности не отображается.</span><span class="sxs-lookup"><span data-stu-id="2aec8-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="2aec8-151">Первичные ключи для пользователей обычно не имеют значения.</span><span class="sxs-lookup"><span data-stu-id="2aec8-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="2aec8-152">В этом случае в качестве первичного ключа используется номер курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="2aec8-153">Изменяет заголовок для раскрывающегося списка кафедр с **DepartmentID** на **Department**.</span><span class="sxs-lookup"><span data-stu-id="2aec8-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2aec8-154">Заменяет `"ViewBag.DepartmentID"` на `DepartmentNameSL` (из базового класса).</span><span class="sxs-lookup"><span data-stu-id="2aec8-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2aec8-155">На этой странице содержится скрытое поле (`<input type="hidden">`) с номером курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="2aec8-156">Добавление вспомогательной функции тега `<label>` с `asp-for="Course.CourseID"` не избавляет от необходимости использовать это скрытое поле.</span><span class="sxs-lookup"><span data-stu-id="2aec8-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="2aec8-157">`<input type="hidden">` необходимо, чтобы включить номер курса в отправляемые данные при нажатии пользователем кнопки **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="2aec8-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="2aec8-158">Обновление страниц сведений и удаления курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="2aec8-159">Применение [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) позволяет повысить производительность в тех сценариях, где не требуется отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2aec8-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="2aec8-160">Обновление моделей для страниц курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-160">Update the Course page models</span></span>

<span data-ttu-id="2aec8-161">Измените файл *Pages/Courses/Delete.cshtml.cs*, используя следующий код, чтобы добавить `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="2aec8-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="2aec8-162">Внесите такое же изменение в файл *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2aec8-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="2aec8-163">Обновление страницы Razor курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-163">Update the Course Razor pages</span></span>

<span data-ttu-id="2aec8-164">Измените файл *Pages/Courses/Delete.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="2aec8-165">Выполните те же изменения для страницы Details.</span><span class="sxs-lookup"><span data-stu-id="2aec8-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="2aec8-166">Тестирование страниц курса</span><span class="sxs-lookup"><span data-stu-id="2aec8-166">Test the Course pages</span></span>

<span data-ttu-id="2aec8-167">Протестируйте страницы создания, редактирования, сведений и удаления.</span><span class="sxs-lookup"><span data-stu-id="2aec8-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="2aec8-168">Обновление страниц создания и редактирования преподавателей</span><span class="sxs-lookup"><span data-stu-id="2aec8-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="2aec8-169">Преподаватели могут вести любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="2aec8-170">На рисунке ниже показана страница редактирования преподавателей с массивом флажков курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="2aec8-172">С помощью флажков можно изменять курсы, которым назначен преподаватель.</span><span class="sxs-lookup"><span data-stu-id="2aec8-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="2aec8-173">Флажок отображается для каждого курса в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="2aec8-174">Для курсов, которым назначен преподаватель, флажок установлен.</span><span class="sxs-lookup"><span data-stu-id="2aec8-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="2aec8-175">Пользователь может устанавливать и снимать флажки, изменяя назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="2aec8-176">Если число курсов было бы гораздо больше, лучше подошел бы другой пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="2aec8-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="2aec8-177">Однако представленный здесь способ управления связями "многие ко многим" был бы тем же.</span><span class="sxs-lookup"><span data-stu-id="2aec8-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="2aec8-178">Для создания или удаления связей производятся операции с сущностью соединения.</span><span class="sxs-lookup"><span data-stu-id="2aec8-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="2aec8-179">Создание класса для данных по назначенным курсам</span><span class="sxs-lookup"><span data-stu-id="2aec8-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="2aec8-180">Создайте файл *SchoolViewModels/AssignedCourseData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="2aec8-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="2aec8-181">Класс `AssignedCourseData` содержит данные для создания флажков, определяющих назначенные преподавателю курсы.</span><span class="sxs-lookup"><span data-stu-id="2aec8-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="2aec8-182">Создание базового класса модели для страницы преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="2aec8-183">Создайте базовый класс *Pages/Instructors/InstructorCoursesPageModel.cs*:</span><span class="sxs-lookup"><span data-stu-id="2aec8-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="2aec8-184">Базовый класс `InstructorCoursesPageModel` будет использоваться для моделей страниц редактирования и создания.</span><span class="sxs-lookup"><span data-stu-id="2aec8-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="2aec8-185">`PopulateAssignedCourseData` считывает все сущности `Course` для заполнения списка `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="2aec8-186">Для каждого курса код задает `CourseID`, название, а также сведения о назначении курсу преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="2aec8-187">Для эффективного поиска используется класс [HashSet](/dotnet/api/system.collections.generic.hashset-1).</span><span class="sxs-lookup"><span data-stu-id="2aec8-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="2aec8-188">Так как страница Razor не содержит коллекцию сущностей Course, связыватель модели не может автоматически обновить свойство навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2aec8-189">Вместо использования связывателя модели для обновления свойства навигации `CourseAssignments` вы делаете это в новом методе `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="2aec8-190">Поэтому нужно исключить свойство `CourseAssignments` из привязки модели.</span><span class="sxs-lookup"><span data-stu-id="2aec8-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="2aec8-191">Это не требует внесения никаких изменений в код, вызывающем `TryUpdateModel`, так как вы используете перегрузку на базе списка разрешений, а `CourseAssignments` отсутствует в списке включений.</span><span class="sxs-lookup"><span data-stu-id="2aec8-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="2aec8-192">Если никакие флажки не выбраны, код в `UpdateInstructorCourses` инициализирует свойство навигации `CourseAssignments` с использованием пустой коллекции и возвращает следующее:</span><span class="sxs-lookup"><span data-stu-id="2aec8-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="2aec8-193">После этого код в цикле проходит по всем курсам в базе данных и сравнивает каждый из них с теми, которые сейчас назначены преподавателю, в противоположность тем, которые были выбраны на странице.</span><span class="sxs-lookup"><span data-stu-id="2aec8-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="2aec8-194">Чтобы упростить эффективную подстановку, последние две коллекции хранятся в объектах `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="2aec8-195">Если флажок для курса был установлен, но курс отсутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс добавляется в коллекцию в свойстве навигации.</span><span class="sxs-lookup"><span data-stu-id="2aec8-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="2aec8-196">Если флажок для курса не был установлен, но курс присутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс удаляется из свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="2aec8-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="2aec8-197">Обработка расположения кабинета</span><span class="sxs-lookup"><span data-stu-id="2aec8-197">Handle office location</span></span>

<span data-ttu-id="2aec8-198">Еще одна связь, с которой должна работать страница редактирования, — это связь "один к нулю или к одному" между сущностью Instructor и сущностью `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="2aec8-199">Код изменения преподавателя должен обеспечивать указанные ниже сценарии.</span><span class="sxs-lookup"><span data-stu-id="2aec8-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="2aec8-200">Если пользователь удаляет назначение кабинета, удаляется сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2aec8-201">Если пользователь вводит назначение пустого кабинета, создается новая сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2aec8-202">Если пользователь изменяет назначение кабинета, обновляется сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="2aec8-203">Обновление модели для страницы редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="2aec8-204">Измените файл *Pages/Instructors/Edit.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="2aec8-205">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-205">The preceding code:</span></span>

* <span data-ttu-id="2aec8-206">Получает текущую сущность `Instructor` из базы данных, используя безотложную загрузку для свойств навигации `OfficeAssignment`, `CourseAssignment` и `CourseAssignment.Course`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="2aec8-207">Обновляет извлеченную сущность `Instructor`, используя значения из связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="2aec8-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="2aec8-208">`TryUpdateModel` позволяет предотвратить [чрезмерную передачу данных](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2aec8-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2aec8-209">Если расположение кабинета пусто, `Instructor.OfficeAssignment` получает значение NULL.</span><span class="sxs-lookup"><span data-stu-id="2aec8-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="2aec8-210">Если `Instructor.OfficeAssignment` имеет значение NULL, связанная строка в таблице `OfficeAssignment` удаляется.</span><span class="sxs-lookup"><span data-stu-id="2aec8-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="2aec8-211">Вызывает `PopulateAssignedCourseData` в `OnGetAsync`, чтобы предоставить сведения для флажков с помощью класса модели представления `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="2aec8-212">Вызывает `UpdateInstructorCourses` в `OnPostAsync`, чтобы применить сведения флажков к редактируемой сущности Instructor.</span><span class="sxs-lookup"><span data-stu-id="2aec8-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="2aec8-213">Вызывает `PopulateAssignedCourseData` и `UpdateInstructorCourses` в `OnPostAsync` в случае сбоя `TryUpdateModel`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="2aec8-214">Эти вызовы методов восстанавливают данные по назначенным курсам, введенные на странице, при ее повторном отображении с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="2aec8-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="2aec8-215">Обновление страницы Razor редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="2aec8-216">Измените файл *Pages/Instructors/Edit.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="2aec8-217">Приведенный выше код создает таблицу HTML с тремя столбцами.</span><span class="sxs-lookup"><span data-stu-id="2aec8-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="2aec8-218">Каждый столбец содержит флажок и заголовок с номером и названием курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="2aec8-219">Все флажки имеют одинаковые имена ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="2aec8-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="2aec8-220">Поскольку используется одно и то же имя, связыватель модели интерпретирует их как группу.</span><span class="sxs-lookup"><span data-stu-id="2aec8-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="2aec8-221">Атрибуту значения для каждого флажка присвоен `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="2aec8-222">При отправке страницы связыватель модели передает массив, содержащий значения `CourseID` только для установленных флажков.</span><span class="sxs-lookup"><span data-stu-id="2aec8-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="2aec8-223">При первичной отрисовке флажков выбираются курсы, назначенные преподавателю.</span><span class="sxs-lookup"><span data-stu-id="2aec8-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="2aec8-224">Примечание. Описываемый здесь подход к редактированию данных курсов для преподавателя эффективен при ограниченном числе курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="2aec8-225">Для коллекций большего размера более практичным и эффективным было бы применение другого пользовательского интерфейса и другого метода обновления.</span><span class="sxs-lookup"><span data-stu-id="2aec8-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="2aec8-226">Запустите приложение и протестируйте обновленную страницу редактирования преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="2aec8-227">Измените некоторые назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-227">Change some course assignments.</span></span> <span data-ttu-id="2aec8-228">Изменения отражаются на странице указателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="2aec8-229">Обновление страницы создания преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-229">Update the Instructor Create page</span></span>

<span data-ttu-id="2aec8-230">Обновите модель для страницы создания преподавателя и соответствующую страницу Razor, используя код, аналогичный коду страницы редактирования.</span><span class="sxs-lookup"><span data-stu-id="2aec8-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="2aec8-231">Протестируйте страницу создания преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="2aec8-232">Обновление страницы удаления преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="2aec8-233">Измените файл *Pages/Instructors/Delete.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="2aec8-234">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2aec8-235">Использует упреждающую загрузку для свойства навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2aec8-236">Требуется включить `CourseAssignments`, иначе они не будут удалены при удалении преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="2aec8-237">Чтобы избежать необходимости считывать их, настройте каскадное удаление в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="2aec8-238">Если преподаватель, которого требуется удалить, назначен в качестве администратора любой из кафедр, удаляется назначение преподавателя из таких кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="2aec8-239">Запустите приложение и протестируйте страницу удаления.</span><span class="sxs-lookup"><span data-stu-id="2aec8-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2aec8-240">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="2aec8-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2aec8-241">[Предыдущий учебник](xref:data/ef-rp/read-related-data)
> [Следующий учебник](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="2aec8-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2aec8-242">В этом учебнике демонстрируется обновление связанных данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="2aec8-243">При возникновении проблем, которые вам не удается устранить, [скачайте или просмотрите готовое приложение.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="2aec8-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="2aec8-244">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2aec8-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="2aec8-245">На следующих рисунках изображены некоторые готовые страницы.</span><span class="sxs-lookup"><span data-stu-id="2aec8-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="2aec8-246">![Страница редактирования курса](update-related-data/_static/course-edit.png)
![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="2aec8-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="2aec8-247">Просмотрите и протестируйте страницы создания и редактирования курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="2aec8-248">Создайте новый курс.</span><span class="sxs-lookup"><span data-stu-id="2aec8-248">Create a new course.</span></span> <span data-ttu-id="2aec8-249">Кафедра выбирается по целочисленному первичному ключу, а не по названию.</span><span class="sxs-lookup"><span data-stu-id="2aec8-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="2aec8-250">Измените новый курс.</span><span class="sxs-lookup"><span data-stu-id="2aec8-250">Edit the new course.</span></span> <span data-ttu-id="2aec8-251">После завершения тестирования удалите новый курс.</span><span class="sxs-lookup"><span data-stu-id="2aec8-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="2aec8-252">Создание базового класса для совместного использования общего кода</span><span class="sxs-lookup"><span data-stu-id="2aec8-252">Create a base class to share common code</span></span>

<span data-ttu-id="2aec8-253">На страницах Courses/Create и Courses/Edit используется список названий кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="2aec8-254">Создайте базовый класс *Pages/Courses/DepartmentNamePageModel.cshtml.cs* для страниц Create и Edit:</span><span class="sxs-lookup"><span data-stu-id="2aec8-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="2aec8-255">Приведенный выше код создает [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), содержащий список названий кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="2aec8-256">Если указан параметр `selectedDepartment`, кафедра выбрана в списке `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="2aec8-257">Классы моделей страниц Create и Edit являются производными от `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="2aec8-258">Страницы настройки курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-258">Customize the Courses Pages</span></span>

<span data-ttu-id="2aec8-259">Создаваемая сущность курса должна иметь связь с существующей кафедрой.</span><span class="sxs-lookup"><span data-stu-id="2aec8-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="2aec8-260">Чтобы добавить кафедру при создании курса, в базовом классе для страниц Create и Edit реализован раскрывающийся список, в котором можно выбрать кафедру.</span><span class="sxs-lookup"><span data-stu-id="2aec8-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="2aec8-261">Этот раскрывающийся список устанавливает свойство внешнего ключа `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="2aec8-262">Платформа EF Core использует внешний ключ `Course.DepartmentID` для загрузки свойства навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Создание курса](update-related-data/_static/ddl.png)

<span data-ttu-id="2aec8-264">Обновите модель страницы Create, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="2aec8-265">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-265">The preceding code:</span></span>

* <span data-ttu-id="2aec8-266">Происходит от `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="2aec8-267">Использует `TryUpdateModelAsync`, чтобы предотвратить [чрезмерную передачу данных](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2aec8-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2aec8-268">Заменяет `ViewData["DepartmentID"]` на `DepartmentNameSL` (из базового класса).</span><span class="sxs-lookup"><span data-stu-id="2aec8-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2aec8-269">`ViewData["DepartmentID"]` заменяется строго типизированным `DepartmentNameSL`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="2aec8-270">Вместо слабо типизированных моделей рекомендуется использовать строго типизированные.</span><span class="sxs-lookup"><span data-stu-id="2aec8-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="2aec8-271">Дополнительные сведения см. в разделе [Слабо типизированные данные (ViewData и ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="2aec8-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="2aec8-272">Обновление страницы создания курсов</span><span class="sxs-lookup"><span data-stu-id="2aec8-272">Update the Courses Create page</span></span>

<span data-ttu-id="2aec8-273">Измените файл *Pages/Courses/Create.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="2aec8-274">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="2aec8-275">Изменяет заголовок с **DepartmentID** на **Department**.</span><span class="sxs-lookup"><span data-stu-id="2aec8-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2aec8-276">Заменяет `"ViewBag.DepartmentID"` на `DepartmentNameSL` (из базового класса).</span><span class="sxs-lookup"><span data-stu-id="2aec8-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="2aec8-277">Добавляет параметр "Select Department" (Выбор кафедры).</span><span class="sxs-lookup"><span data-stu-id="2aec8-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="2aec8-278">В результате этого изменения вместо первой кафедры отображается параметр "Select Department" (Выбор кафедры).</span><span class="sxs-lookup"><span data-stu-id="2aec8-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="2aec8-279">Добавляет сообщение о проверке в том случае, если не выбрана кафедра.</span><span class="sxs-lookup"><span data-stu-id="2aec8-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="2aec8-280">На странице Razor Pages используется [вспомогательная функция тега Select](xref:mvc/views/working-with-forms#the-select-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2aec8-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="2aec8-281">Протестируйте страницу создания.</span><span class="sxs-lookup"><span data-stu-id="2aec8-281">Test the Create page.</span></span> <span data-ttu-id="2aec8-282">На странице Create отображается название, а не идентификатор кафедры.</span><span class="sxs-lookup"><span data-stu-id="2aec8-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="2aec8-283">Обновите страницу редактирования курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="2aec8-284">Замените код в файле *Pages/Courses/Edit.cshtml.cs* на приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="2aec8-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="2aec8-285">Изменения аналогичны внесенным в модель страницы Create.</span><span class="sxs-lookup"><span data-stu-id="2aec8-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="2aec8-286">В приведенном выше коде `PopulateDepartmentsDropDownList` передает идентификатор кафедры, по которому выбирается кафедра, заданная в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="2aec8-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="2aec8-287">Обновите файл *Pages/Courses/Edit.cshtml*, используя следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="2aec8-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="2aec8-288">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="2aec8-289">Отображает идентификатор курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-289">Displays the course ID.</span></span> <span data-ttu-id="2aec8-290">Как правило, первичный ключ сущности не отображается.</span><span class="sxs-lookup"><span data-stu-id="2aec8-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="2aec8-291">Первичные ключи для пользователей обычно не имеют значения.</span><span class="sxs-lookup"><span data-stu-id="2aec8-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="2aec8-292">В этом случае в качестве первичного ключа используется номер курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="2aec8-293">Изменяет заголовок с **DepartmentID** на **Department**.</span><span class="sxs-lookup"><span data-stu-id="2aec8-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2aec8-294">Заменяет `"ViewBag.DepartmentID"` на `DepartmentNameSL` (из базового класса).</span><span class="sxs-lookup"><span data-stu-id="2aec8-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2aec8-295">На этой странице содержится скрытое поле (`<input type="hidden">`) с номером курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="2aec8-296">Добавление вспомогательной функции тега `<label>` с `asp-for="Course.CourseID"` не избавляет от необходимости использовать это скрытое поле.</span><span class="sxs-lookup"><span data-stu-id="2aec8-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="2aec8-297">`<input type="hidden">` необходимо, чтобы включить номер курса в отправляемые данные при нажатии пользователем кнопки **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="2aec8-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="2aec8-298">Протестируйте обновленный код.</span><span class="sxs-lookup"><span data-stu-id="2aec8-298">Test the updated code.</span></span> <span data-ttu-id="2aec8-299">Создайте, измените и удалите курс.</span><span class="sxs-lookup"><span data-stu-id="2aec8-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="2aec8-300">Добавление AsNoTracking в модели страниц Details и Delete</span><span class="sxs-lookup"><span data-stu-id="2aec8-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="2aec8-301">Применение [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) позволяет повысить производительность в тех сценариях, где не требуется отслеживание.</span><span class="sxs-lookup"><span data-stu-id="2aec8-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="2aec8-302">Добавьте `AsNoTracking` в модели страниц Delete и Details.</span><span class="sxs-lookup"><span data-stu-id="2aec8-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="2aec8-303">Следующий код отображает обновленную модель страницы Delete:</span><span class="sxs-lookup"><span data-stu-id="2aec8-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="2aec8-304">Обновите метод `OnGetAsync` в файле *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2aec8-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="2aec8-305">Изменение страниц Delete и Details</span><span class="sxs-lookup"><span data-stu-id="2aec8-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="2aec8-306">Обновите страницу Razor Delete, используя следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="2aec8-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="2aec8-307">Выполните те же изменения для страницы Details.</span><span class="sxs-lookup"><span data-stu-id="2aec8-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="2aec8-308">Тестирование страниц курса</span><span class="sxs-lookup"><span data-stu-id="2aec8-308">Test the Course pages</span></span>

<span data-ttu-id="2aec8-309">Протестируйте страницы создания, редактирования и сведений, после чего удалите их.</span><span class="sxs-lookup"><span data-stu-id="2aec8-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="2aec8-310">Обновление страниц преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-310">Update the instructor pages</span></span>

<span data-ttu-id="2aec8-311">В следующих разделах обновляются страницы преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="2aec8-312">Добавить расположения кабинета</span><span class="sxs-lookup"><span data-stu-id="2aec8-312">Add office location</span></span>

<span data-ttu-id="2aec8-313">При редактировании записи преподавателя может потребоваться обновить назначенный преподавателю кабинет.</span><span class="sxs-lookup"><span data-stu-id="2aec8-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="2aec8-314">Сущность `Instructor` имеет отношение "один к нулю или к одному" с сущностью `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="2aec8-315">Код преподавателя должен обрабатывать следующее:</span><span class="sxs-lookup"><span data-stu-id="2aec8-315">The instructor code must handle:</span></span>

* <span data-ttu-id="2aec8-316">Если пользователь удаляет назначение кабинета, удаляется сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2aec8-317">Если пользователь вводит назначение пустого кабинета, создается новая сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2aec8-318">Если пользователь изменяет назначение кабинета, обновляется сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="2aec8-319">Обновите модель страницы редактирования преподавателей, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="2aec8-320">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-320">The preceding code:</span></span>

* <span data-ttu-id="2aec8-321">Получает текущую сущность `Instructor` из базы данных, используя безотложную загрузку для свойства навигации `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="2aec8-322">Обновляет извлеченную сущность `Instructor`, используя значения из связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="2aec8-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="2aec8-323">`TryUpdateModel` позволяет предотвратить [чрезмерную передачу данных](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2aec8-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2aec8-324">Если расположение кабинета пусто, `Instructor.OfficeAssignment` получает значение NULL.</span><span class="sxs-lookup"><span data-stu-id="2aec8-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="2aec8-325">Если `Instructor.OfficeAssignment` имеет значение NULL, связанная строка в таблице `OfficeAssignment` удаляется.</span><span class="sxs-lookup"><span data-stu-id="2aec8-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="2aec8-326">Обновление страницы редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-326">Update the instructor Edit page</span></span>

<span data-ttu-id="2aec8-327">Обновите файл *Pages/Instructors/Edit.cshtml*, указав расположение кабинета:</span><span class="sxs-lookup"><span data-stu-id="2aec8-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="2aec8-328">Убедитесь, что можно изменить расположение кабинета для преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="2aec8-329">Добавление назначений курсов на страницу редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="2aec8-330">Преподаватели могут вести любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="2aec8-331">В этом разделе вы добавите возможность изменять назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="2aec8-332">На следующем рисунке показана обновленная страница редактирования преподавателя:</span><span class="sxs-lookup"><span data-stu-id="2aec8-332">The following image shows the updated instructor Edit page:</span></span>

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="2aec8-334">Между сущностями `Course` и `Instructor` существует отношение "многие ко многим".</span><span class="sxs-lookup"><span data-stu-id="2aec8-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="2aec8-335">Для добавления и удаления отношений можно добавлять сущности в список объединенного набора сущностей `CourseAssignments` и удалять их из него.</span><span class="sxs-lookup"><span data-stu-id="2aec8-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="2aec8-336">С помощью флажков можно изменять курсы, которым назначен преподаватель.</span><span class="sxs-lookup"><span data-stu-id="2aec8-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="2aec8-337">Флажок отображается для каждого курса в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="2aec8-338">Для курсов, которым назначен преподаватель, флажок установлен.</span><span class="sxs-lookup"><span data-stu-id="2aec8-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="2aec8-339">Пользователь может устанавливать и снимать флажки, изменяя назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="2aec8-340">Если число курсов было бы гораздо больше:</span><span class="sxs-lookup"><span data-stu-id="2aec8-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="2aec8-341">В таком сценарии, скорее всего, применялся бы другой пользовательский интерфейс для отображения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="2aec8-342">Способ управления объединением сущностей для создания или удаления отношений при этом не изменился бы.</span><span class="sxs-lookup"><span data-stu-id="2aec8-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="2aec8-343">Добавление классов для поддержки страниц создания и редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="2aec8-344">Создайте файл *SchoolViewModels/AssignedCourseData.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="2aec8-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="2aec8-345">Класс `AssignedCourseData` содержит данные для создания флажков, определяющих назначенные преподавателю курсы.</span><span class="sxs-lookup"><span data-stu-id="2aec8-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="2aec8-346">Создайте базовый класс *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2aec8-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="2aec8-347">Базовый класс `InstructorCoursesPageModel` будет использоваться для моделей страниц редактирования и создания.</span><span class="sxs-lookup"><span data-stu-id="2aec8-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="2aec8-348">`PopulateAssignedCourseData` считывает все сущности `Course` для заполнения списка `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="2aec8-349">Для каждого курса код задает `CourseID`, название, а также сведения о назначении курсу преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="2aec8-350">Для реализации эффективного поиска используется класс [HashSet](/dotnet/api/system.collections.generic.hashset-1).</span><span class="sxs-lookup"><span data-stu-id="2aec8-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="2aec8-351">Модель страницы редактирования преподавателей</span><span class="sxs-lookup"><span data-stu-id="2aec8-351">Instructors Edit page model</span></span>

<span data-ttu-id="2aec8-352">Обновите модель страницы редактирования преподавателя, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="2aec8-353">Приведенный выше код обрабатывает изменения в назначении кабинета.</span><span class="sxs-lookup"><span data-stu-id="2aec8-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="2aec8-354">Обновление представлений Razor преподавателя</span><span class="sxs-lookup"><span data-stu-id="2aec8-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="2aec8-355">При вставке кода в Visual Studio разрывы строк изменяются, нарушая код.</span><span class="sxs-lookup"><span data-stu-id="2aec8-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="2aec8-356">Один раз нажмите клавиши CTRL+Z, чтобы отменить автоматическое форматирование.</span><span class="sxs-lookup"><span data-stu-id="2aec8-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="2aec8-357">При нажатии клавиш CTRL+Z разрывы строк исправляются, благодаря чему код приобретает показанный здесь вид.</span><span class="sxs-lookup"><span data-stu-id="2aec8-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="2aec8-358">Выравнивать отступы необязательно, однако строки `@:</tr><tr>`, `@:<td>`, `@:</td>` и `@:</tr>` должны находиться на одной строке, как показано здесь.</span><span class="sxs-lookup"><span data-stu-id="2aec8-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="2aec8-359">Выделите блок нового кода и три раза нажмите клавишу TAB, чтобы выровнять его с существующим кодом.</span><span class="sxs-lookup"><span data-stu-id="2aec8-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="2aec8-360">Чтобы проголосовать за эту ошибку или проверить ее статус, воспользуйтесь [этой ссылкой](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="2aec8-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="2aec8-361">Приведенный выше код создает таблицу HTML с тремя столбцами.</span><span class="sxs-lookup"><span data-stu-id="2aec8-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="2aec8-362">Каждый столбец содержит флажок и заголовок с номером и названием курса.</span><span class="sxs-lookup"><span data-stu-id="2aec8-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="2aec8-363">Все флажки имеют одинаковые имена ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="2aec8-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="2aec8-364">Поскольку используется одно и то же имя, связыватель модели интерпретирует их как группу.</span><span class="sxs-lookup"><span data-stu-id="2aec8-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="2aec8-365">Атрибуту значения для каждого флажка присвоен `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="2aec8-366">При отправке страницы связыватель модели передает массив, содержащий значения `CourseID` только для выбранных флажков.</span><span class="sxs-lookup"><span data-stu-id="2aec8-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="2aec8-367">При первичной отрисовке для курсов, назначенных преподавателю, отображаются установленные флажки.</span><span class="sxs-lookup"><span data-stu-id="2aec8-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="2aec8-368">Запустите приложение и протестируйте обновленную страницу редактирования преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="2aec8-369">Измените некоторые назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-369">Change some course assignments.</span></span> <span data-ttu-id="2aec8-370">Изменения отражаются на странице указателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="2aec8-371">Примечание. Описываемый здесь подход к редактированию данных курсов для преподавателя эффективен при ограниченном числе курсов.</span><span class="sxs-lookup"><span data-stu-id="2aec8-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="2aec8-372">Для коллекций большего размера более практичным и эффективным было бы применение другого пользовательского интерфейса и другого метода обновления.</span><span class="sxs-lookup"><span data-stu-id="2aec8-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="2aec8-373">Обновление страницы создания преподавателей</span><span class="sxs-lookup"><span data-stu-id="2aec8-373">Update the instructors Create page</span></span>

<span data-ttu-id="2aec8-374">Обновите модель страницы создания преподавателя, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="2aec8-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="2aec8-375">Приведенный выше код аналогичен коду в файле *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2aec8-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="2aec8-376">Обновите страницу создания преподавателя Razor, используя следующую разметку.</span><span class="sxs-lookup"><span data-stu-id="2aec8-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="2aec8-377">Протестируйте страницу создания преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2aec8-378">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="2aec8-378">Update the Delete page</span></span>

<span data-ttu-id="2aec8-379">Обновите страницу "Delete" (Удаление) с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="2aec8-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="2aec8-380">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="2aec8-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2aec8-381">Использует упреждающую загрузку для свойства навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2aec8-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2aec8-382">Требуется включить `CourseAssignments`, иначе они не будут удалены при удалении преподавателя.</span><span class="sxs-lookup"><span data-stu-id="2aec8-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="2aec8-383">Чтобы избежать необходимости считывать их, настройте каскадное удаление в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2aec8-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="2aec8-384">Если преподаватель, которого требуется удалить, назначен в качестве администратора любой из кафедр, удаляется назначение преподавателя из таких кафедр.</span><span class="sxs-lookup"><span data-stu-id="2aec8-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2aec8-385">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2aec8-385">Additional resources</span></span>

* [<span data-ttu-id="2aec8-386">Версия руководства на YouTube (часть 1)</span><span class="sxs-lookup"><span data-stu-id="2aec8-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="2aec8-387">Версия руководства на YouTube (часть 2)</span><span class="sxs-lookup"><span data-stu-id="2aec8-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="2aec8-388">[Назад](xref:data/ef-rp/read-related-data)
> [Вперед](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="2aec8-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
