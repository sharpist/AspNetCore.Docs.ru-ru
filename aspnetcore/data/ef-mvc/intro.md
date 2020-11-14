---
title: Учебник. Начало работы с EF Core в веб-приложении MVC ASP.NET
description: Это первый учебник из серии, в котором описывается создание примера приложения EF/MVC для университета Contoso.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365388"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="62981-103">Учебник. Начало работы с EF Core в веб-приложении MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="62981-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="62981-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="62981-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="62981-105">На примере веб-приложения для университета Contoso демонстрируется процесс создания веб-приложений ASP.NET Core MVC с помощью Entity Framework (EF) Core и Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62981-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="62981-106">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="62981-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="62981-107">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="62981-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="62981-108">Это первый учебник из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="62981-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62981-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="62981-109">Prerequisites</span></span>

* <span data-ttu-id="62981-110">Если у вас нет опыта работы с ASP.NET Core MVC, ознакомьтесь с серией учебников по [началу работы с ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc), прежде чем приступать к изучению этого учебника.</span><span class="sxs-lookup"><span data-stu-id="62981-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="62981-111">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="62981-111">Database engines</span></span>

<span data-ttu-id="62981-112">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="62981-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="62981-113">Решение проблем и устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="62981-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="62981-114">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="62981-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="62981-115">Список распространенных ошибок и способы их устранения см. в [разделе "Устранение неполадок" последнего руководства серии](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="62981-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="62981-116">Если вам не удалось найти нужную информацию, вы можете задать вопрос на сайте StackOverflow.com в разделах, посвященных [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="62981-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="62981-117">Эта серия включает в себя 10 учебников, содержание каждого из которых базируется на предыдущих учебниках.</span><span class="sxs-lookup"><span data-stu-id="62981-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="62981-118">После успешного завершения каждого руководства рекомендуется сохранять копию проекта.</span><span class="sxs-lookup"><span data-stu-id="62981-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="62981-119">Таким образом, при возникновении проблем вы сможете вернуться к предыдущему учебнику, а не к началу серии.</span><span class="sxs-lookup"><span data-stu-id="62981-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="62981-120">Веб-приложение Contoso University</span><span class="sxs-lookup"><span data-stu-id="62981-120">Contoso University web app</span></span>

<span data-ttu-id="62981-121">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="62981-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="62981-122">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="62981-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="62981-123">Вот несколько экранов в приложении:</span><span class="sxs-lookup"><span data-stu-id="62981-123">Here are a few of the screens in the app:</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="62981-126">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="62981-126">Create web app</span></span>

* <span data-ttu-id="62981-127">Запустите Visual Studio и выберите **Веб-приложение ASP.NET Core**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="62981-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="62981-128">Задайте для проекта имя `ContosoUniversity`.</span><span class="sxs-lookup"><span data-stu-id="62981-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="62981-129">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="62981-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="62981-130">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="62981-130">Select **Create**.</span></span>
* <span data-ttu-id="62981-131">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 5.0** , а затем — шаблон **Веб-приложение (модель-представление-контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="62981-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="62981-132">![Диалоговое окно "Создание проекта ASP.NET Core"](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="62981-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="62981-133">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="62981-133">Set up the site style</span></span>

<span data-ttu-id="62981-134">Выполните небольшую базовую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="62981-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="62981-135">Откройте файл *Views/Shared/_Layout.cshtml* и внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="62981-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="62981-136">Измените каждое вхождение `ContosoUniversity` на `Contoso University`.</span><span class="sxs-lookup"><span data-stu-id="62981-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="62981-137">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="62981-137">There are three occurrences.</span></span>
* <span data-ttu-id="62981-138">Добавьте пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="62981-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="62981-139">Предыдущие изменения выделены в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="62981-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="62981-140">Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="62981-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="62981-141">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="62981-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="62981-142">Домашняя страница отображается с вкладками для страниц, созданных при работе с этим учебником.</span><span class="sxs-lookup"><span data-stu-id="62981-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Домашняя страница университета Contoso](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="62981-144">Пакеты NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="62981-144">EF Core NuGet packages</span></span>

<span data-ttu-id="62981-145">В этом учебнике используется SQL Server, для которого требуется пакет поставщика [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="62981-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="62981-146">Этот пакет EF SQL Server и его зависимости, `Microsoft.EntityFrameworkCore` и `Microsoft.EntityFrameworkCore.Relational`, обеспечивают поддержку среды выполнения для платформы EF.</span><span class="sxs-lookup"><span data-stu-id="62981-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="62981-147">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) и пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="62981-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="62981-148">Введите в консоли диспетчера программ следующие команды, чтобы добавить пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="62981-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="62981-149">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок EF Core.</span><span class="sxs-lookup"><span data-stu-id="62981-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="62981-150">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций EF Core.</span><span class="sxs-lookup"><span data-stu-id="62981-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="62981-151">Дополнительные сведения о других поставщиках баз данных, которые доступны для платформы EF Core, см. в разделе [Поставщики баз данных](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="62981-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="62981-152">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="62981-152">Create the data model</span></span>

<span data-ttu-id="62981-153">Для этого приложения будут созданы следующие классы сущностей:</span><span class="sxs-lookup"><span data-stu-id="62981-153">The following entity classes are created for this app:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="62981-155">У приведенных выше сущностей есть следующие отношения.</span><span class="sxs-lookup"><span data-stu-id="62981-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="62981-156">Между сущностями `Student` и `Enrollment` действует связь "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="62981-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="62981-157">Учащегося можно зачислить на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="62981-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="62981-158">Между сущностями `Course` и `Enrollment` действует связь "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="62981-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="62981-159">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="62981-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="62981-160">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="62981-161">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="62981-161">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="62981-163">В папке *Models* создайте класс `Student`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="62981-164">Свойство `ID` используется в качестве столбца первичного ключа ( **ПК** ) в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="62981-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="62981-165">По умолчанию платформа EF интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="62981-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="62981-166">Например, ПК может называться `StudentID`, а не `ID`.</span><span class="sxs-lookup"><span data-stu-id="62981-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="62981-167">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="62981-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="62981-168">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="62981-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="62981-169">Свойство `Enrollments` сущности `Student`:</span><span class="sxs-lookup"><span data-stu-id="62981-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="62981-170">Содержит все сущности `Enrollment`, связанные с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="62981-171">Если определенная строка `Student` в базе данных содержит две связанные строки `Enrollment`:</span><span class="sxs-lookup"><span data-stu-id="62981-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="62981-172">Свойство навигации `Enrollments` сущности `Student` содержит эти две сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="62981-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="62981-173">Строки `Enrollment` содержат значение первичного ключа учащегося в столбце внешнего ключа ( **ВК** ) `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="62981-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="62981-174">Если свойство навигации содержит несколько сущностей:</span><span class="sxs-lookup"><span data-stu-id="62981-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="62981-175">Тип должен быть списком, например `ICollection<T>`, `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="62981-176">Сущности можно добавлять, удалять и обновлять.</span><span class="sxs-lookup"><span data-stu-id="62981-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="62981-177">Связи "многие ко многим" и "один ко многим" могут содержать несколько сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="62981-178">Если используется `ICollection<T>`, платформа EF по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="62981-179">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="62981-179">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="62981-181">В папке *Models* создайте класс `Enrollment`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="62981-182">Свойство `EnrollmentID` — это ПК.</span><span class="sxs-lookup"><span data-stu-id="62981-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="62981-183">Эта сущность использует шаблон `classnameID` вместо самого по себе `ID`.</span><span class="sxs-lookup"><span data-stu-id="62981-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="62981-184">Сущность `Student` использовала шаблон `ID`.</span><span class="sxs-lookup"><span data-stu-id="62981-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="62981-185">Некоторые разработчики предпочитают использовать один шаблон во всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="62981-186">В этом учебнике демонстрируется возможность использования любого из шаблонов.</span><span class="sxs-lookup"><span data-stu-id="62981-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="62981-187">В [одном из следующих учебников](inheritance.md) показано, за счет чего использование `ID` без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="62981-188">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="62981-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="62981-189">Знак `?` после объявления типа `Grade` указывает, что свойство `Grade` [допускает значение NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span><span class="sxs-lookup"><span data-stu-id="62981-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="62981-190">Оценка со значением `null` отличается от нулевой оценки.</span><span class="sxs-lookup"><span data-stu-id="62981-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="62981-191">При значении `null` оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="62981-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="62981-192">Свойство `StudentID` представляет собой внешний ключ (ВК). Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="62981-193">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит только отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="62981-194">Она отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="62981-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="62981-195">Свойство `CourseID` представляет собой ВК. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="62981-196">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="62981-197">Entity Framework интерпретирует свойство как свойство ВК, если оно называется `<`имя_свойства_навигации`><`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="62981-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="62981-198">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет значение ПК `ID`.</span><span class="sxs-lookup"><span data-stu-id="62981-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="62981-199">Свойства ВК также могут называться `<`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="62981-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="62981-200">Например, `CourseID`, так как сущность `Course` имеет значение ПК `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="62981-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="62981-201">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="62981-201">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="62981-203">В папке *Models* создайте класс `Course`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="62981-204">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="62981-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="62981-205">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="62981-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="62981-206">Атрибут [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) описан в [следующем учебнике](complex-data-model.md).</span><span class="sxs-lookup"><span data-stu-id="62981-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="62981-207">Этот атрибут позволяет ввести ПК для курса, а не использовать базу данных, чтобы создать его.</span><span class="sxs-lookup"><span data-stu-id="62981-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="62981-208">Создание контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="62981-208">Create the database context</span></span>

<span data-ttu-id="62981-209">Контекст базы данных <xref:Microsoft.EntityFrameworkCore.DbContext> — это основной класс, который координирует функциональные возможности EF для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="62981-210">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="62981-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="62981-211">Производный класс `DbContext` указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="62981-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="62981-212">Некоторые расширения функциональности EF можно настроить.</span><span class="sxs-lookup"><span data-stu-id="62981-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="62981-213">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="62981-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="62981-214">В папке проекта создайте папку `Data`.</span><span class="sxs-lookup"><span data-stu-id="62981-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="62981-215">В папке *Data* создайте файл `SchoolContext`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="62981-216">Представленный выше код создает свойство `DbSet` для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="62981-217">Терминология EF:</span><span class="sxs-lookup"><span data-stu-id="62981-217">In EF terminology:</span></span>

* <span data-ttu-id="62981-218">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="62981-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="62981-219">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="62981-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62981-220">Инструкции `DbSet<Enrollment>` и `DbSet<Course>` можно опустить. Это не нарушит функциональность.</span><span class="sxs-lookup"><span data-stu-id="62981-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="62981-221">EF включает их неявно, так как</span><span class="sxs-lookup"><span data-stu-id="62981-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="62981-222">сущность `Student` ссылается на сущность `Enrollment`,</span><span class="sxs-lookup"><span data-stu-id="62981-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="62981-223">а сущность `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="62981-224">При создании базы данных платформа EF создает таблицы с именами, соответствующими именам свойств в `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="62981-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="62981-225">Имена свойств для коллекций обычно указаны во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="62981-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="62981-226">Например, используйте идентификатор `Students` вместо `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="62981-227">В среде разработчиков нет единого мнения о том, следует ли использовать имена таблиц во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="62981-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="62981-228">В этих учебниках вместо принятого по умолчанию способа таблицам в `DbContext` присваиваются имена в единственном числе.</span><span class="sxs-lookup"><span data-stu-id="62981-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="62981-229">Чтобы сделать это, добавьте выделенный ниже код после последнего свойства DbSet.</span><span class="sxs-lookup"><span data-stu-id="62981-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="62981-230">Зарегистрируйте `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="62981-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="62981-231">ASP.NET Core поддерживает [внедрение зависимостей](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="62981-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="62981-232">С помощью внедрения зависимостей службы, например контекст базы данных EF, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="62981-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="62981-233">Затем компоненты, которые используют эти службы, например контроллеры MVC, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="62981-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="62981-234">Код конструктора контроллера, который получает экземпляр контекста, будет приведен позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="62981-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="62981-235">Чтобы зарегистрировать `SchoolContext` как службу, откройте файл *Startup.cs* и добавьте выделенные строки в метод `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62981-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="62981-236">Имя строки подключения передается в контекст путем вызова метода для объекта `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="62981-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="62981-237">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="62981-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="62981-238">Откройте файл *appsettings.json* и добавьте строку подключения, как показано в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="62981-238">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="62981-239">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="62981-239">Add the database exception filter</span></span>

<span data-ttu-id="62981-240">Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="62981-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="62981-241">`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="62981-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="62981-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="62981-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="62981-243">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="62981-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="62981-244">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="62981-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="62981-245">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="62981-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="62981-246">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="62981-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="62981-247">Инициализация базы данных с тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="62981-247">Initialize DB with test data</span></span>

<span data-ttu-id="62981-248">EF создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="62981-248">EF creates an empty database.</span></span> <span data-ttu-id="62981-249">В этом разделе добавляется метод, который вызывается после создания базы данных и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="62981-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="62981-250">Метод `EnsureCreated` будет использоваться для автоматического создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="62981-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="62981-251">В [одном из следующих учебников](migrations.md) вы узнаете, как обрабатывать изменения модели с использованием Code First Migrations, что позволяет изменять схему базы данных вместо того, чтобы удалять и повторно создавать ее.</span><span class="sxs-lookup"><span data-stu-id="62981-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="62981-252">В папке *Data* создайте файл с именем `DbInitializer`, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="62981-253">Предыдущий код проверяет, существует ли база данных:</span><span class="sxs-lookup"><span data-stu-id="62981-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="62981-254">если база данных не найдена,</span><span class="sxs-lookup"><span data-stu-id="62981-254">If the database is not found;</span></span>
  * <span data-ttu-id="62981-255">она создается и загружается вместе с тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="62981-255">It is created and loaded with test data.</span></span> <span data-ttu-id="62981-256">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="62981-257">Если база данных найдена, никакие действия не выполняются.</span><span class="sxs-lookup"><span data-stu-id="62981-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="62981-258">Обновите файл *Program.cs* , используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="62981-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="62981-259">Файл *Program.cs* выполняет следующие действия при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="62981-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="62981-260">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="62981-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="62981-261">Вызовите метод `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="62981-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="62981-262">Удалите контекст по завершении работы метода `Initialize`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="62981-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="62981-263">При первом запуске приложения создается база данных, которая заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="62981-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="62981-264">Каждый раз при изменении модели данных:</span><span class="sxs-lookup"><span data-stu-id="62981-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="62981-265">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="62981-265">Delete the database.</span></span>
* <span data-ttu-id="62981-266">Обновите метод заполнения и запустите заново с новой базой данных.</span><span class="sxs-lookup"><span data-stu-id="62981-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="62981-267">В последующих учебниках описывается, как изменить базу данных при изменении модели данных, не прибегая к ее удалению и повторному созданию.</span><span class="sxs-lookup"><span data-stu-id="62981-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="62981-268">При изменении модели данных данные не теряются.</span><span class="sxs-lookup"><span data-stu-id="62981-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="62981-269">Создание контроллера и представлений</span><span class="sxs-lookup"><span data-stu-id="62981-269">Create controller and views</span></span>

<span data-ttu-id="62981-270">Используйте подсистему формирования шаблонов Visual Studio для добавления контроллера и представлений MVC, которые будут использовать платформу EF для запроса данных и их сохранения.</span><span class="sxs-lookup"><span data-stu-id="62981-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="62981-271">Автоматическое создание методов и представлений операций [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (создание, чтение, обновление и удаление) называется формированием шаблонов.</span><span class="sxs-lookup"><span data-stu-id="62981-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="62981-272">В **обозревателе решений** щелкните правой кнопкой мыши папку `Controllers` и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="62981-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="62981-273">В диалоговом окне **Добавление шаблона** :</span><span class="sxs-lookup"><span data-stu-id="62981-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="62981-274">Выберите **Контроллер MVC с представлениями, использующий Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="62981-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="62981-275">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="62981-275">Click **Add**.</span></span> <span data-ttu-id="62981-276">Откроется диалоговое окно **добавления контроллера MVC с представлениями с использованием Entity Framework**. ![Шаблон Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="62981-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="62981-277">В разделе **Класс модели** выберите **Student**.</span><span class="sxs-lookup"><span data-stu-id="62981-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="62981-278">В разделе **Класс контекста данных** выберите **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="62981-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="62981-279">Оставьте предлагаемое по умолчанию имя **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="62981-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="62981-280">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="62981-280">Click **Add**.</span></span>

<span data-ttu-id="62981-281">Подсистема формирования шаблонов Visual Studio создает файл `StudentsController.cs` и набор представлений (файлы `*.cshtml`), которые будут работать с контроллером.</span><span class="sxs-lookup"><span data-stu-id="62981-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="62981-282">Обратите внимание, что контроллер принимает `SchoolContext` в качестве параметра конструктора.</span><span class="sxs-lookup"><span data-stu-id="62981-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="62981-283">Технология внедрения зависимостей ASP.NET Core обеспечивает передачу экземпляра `SchoolContext` в контроллер.</span><span class="sxs-lookup"><span data-stu-id="62981-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="62981-284">Вы настраиваете ее в классе `Startup`.</span><span class="sxs-lookup"><span data-stu-id="62981-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="62981-285">Контроллер содержит метод действия `Index`, который отображает всех учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="62981-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="62981-286">Этот метод получает список учащихся из набора сущностей Students, считывая свойство `Students` экземпляра контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="62981-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="62981-287">Элементы асинхронного программирования в этом коде рассматриваются далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="62981-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="62981-288">Представление *Views/Students/Index.cshtml* отображает этот список в таблице:</span><span class="sxs-lookup"><span data-stu-id="62981-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="62981-289">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="62981-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="62981-290">Перейдите на вкладку Students (Учащиеся), чтобы просмотреть тестовые данные, добавленные методом `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="62981-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="62981-291">В зависимости от размеров окна браузера ссылку на вкладку `Students` можно найти вверху страницы или щелкнув значок навигации в правом верхнем углу.</span><span class="sxs-lookup"><span data-stu-id="62981-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Уменьшенное окно с домашней страницей университета Contoso](intro/_static/home-page-narrow.png)

![Страница указателя учащихся](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="62981-294">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="62981-294">View the database</span></span>

<span data-ttu-id="62981-295">При запуске приложения метод `DbInitializer.Initialize` вызывает `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="62981-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="62981-296">Платформа EF определила, что база данных отсутствует,</span><span class="sxs-lookup"><span data-stu-id="62981-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="62981-297">поэтому создала базу данных.</span><span class="sxs-lookup"><span data-stu-id="62981-297">So it created a database.</span></span>
* <span data-ttu-id="62981-298">Код метода `Initialize` заполняет базу данных данными.</span><span class="sxs-lookup"><span data-stu-id="62981-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="62981-299">Для просмотра базы данных в Visual Studio используйте **Обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="62981-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="62981-300">Выберите **Обозреватель объектов SQL Server** из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62981-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="62981-301">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных**.</span><span class="sxs-lookup"><span data-stu-id="62981-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="62981-302">Выберите запись `ContosoUniversity1` для имени базы данных, которая находится в строке подключения в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="62981-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="62981-303">Разверните узел **Таблицы** , чтобы просмотреть представленные в базе таблицы.</span><span class="sxs-lookup"><span data-stu-id="62981-303">Expand the **Tables** node to see the tables in the database.</span></span>

![Таблицы в SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="62981-305">Щелкните правой кнопкой мыши таблицу **Student** и выберите пункт **Просмотреть данные** , чтобы просмотреть данные в таблице.</span><span class="sxs-lookup"><span data-stu-id="62981-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![Таблица Student в SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="62981-307">Файлы базы данных с расширением `*.mdf` и `*.ldf` располагаются в папке *C:\Users\\\<username>* .</span><span class="sxs-lookup"><span data-stu-id="62981-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="62981-308">Так как `EnsureCreated` вызывается в методе инициализатора, который выполняется при запуске приложения, можно сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="62981-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="62981-309">Внести изменение в класс `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="62981-310">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="62981-310">Delete the database.</span></span>
* <span data-ttu-id="62981-311">Остановить, а затем запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="62981-311">Stop, then start the app.</span></span> <span data-ttu-id="62981-312">База данных автоматически создается повторно в соответствии с изменением.</span><span class="sxs-lookup"><span data-stu-id="62981-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="62981-313">Например, при добавлении свойства `EmailAddress` в класс `Student` во вновь созданной таблице появится новый столбец `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="62981-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="62981-314">В представлении класса не будет отображаться новое свойство `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="62981-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="62981-315">Соглашения</span><span class="sxs-lookup"><span data-stu-id="62981-315">Conventions</span></span>

<span data-ttu-id="62981-316">Объем кода, написанного для создания полной базы данных платформой EF, сведен к минимуму благодаря использованию соглашений EF.</span><span class="sxs-lookup"><span data-stu-id="62981-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="62981-317">В качестве имен таблиц используются имена свойств `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="62981-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="62981-318">Для сущностей, на которые не ссылается свойство `DbSet`, в качестве имен таблиц используются имена классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="62981-319">В качестве имен столбцов используются имена свойств сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="62981-320">Свойства сущности с именами `ID` или `classnameID` распознаются как свойства ПК.</span><span class="sxs-lookup"><span data-stu-id="62981-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="62981-321">Свойство интерпретируется как свойство ВК, если оно называется `<`имя_свойства_навигации`><`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="62981-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="62981-322">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет значение ПК `ID`.</span><span class="sxs-lookup"><span data-stu-id="62981-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="62981-323">Свойства ВК также могут называться `<`имя_свойства_первичного_ключа`>`.</span><span class="sxs-lookup"><span data-stu-id="62981-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="62981-324">Например, `EnrollmentID`, так как сущность `Enrollment` имеет ПК `EnrollmentID`.</span><span class="sxs-lookup"><span data-stu-id="62981-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="62981-325">Стандартное поведение можно переопределить.</span><span class="sxs-lookup"><span data-stu-id="62981-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="62981-326">Например, можно явно задать имена таблиц, как было показано ранее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="62981-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="62981-327">Имена столбцов и любое свойство можно задать в качестве ПК или ВК.</span><span class="sxs-lookup"><span data-stu-id="62981-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="62981-328">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="62981-328">Asynchronous code</span></span>

<span data-ttu-id="62981-329">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="62981-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="62981-330">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="62981-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="62981-331">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="62981-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="62981-332">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="62981-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="62981-333">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="62981-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="62981-334">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="62981-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="62981-335">Во время выполнения асинхронный код использует немного больше служебных ресурсов, однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="62981-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="62981-336">В следующем коде `async`, `Task<T>`, `await` и `ToListAsync` обеспечивают асинхронное выполнение кода.</span><span class="sxs-lookup"><span data-stu-id="62981-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="62981-337">Ключевое слово `async` указывает компилятору создавать обратные вызовы для частей тела метода и автоматически создавать возвращаемый объект `Task<IActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="62981-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="62981-338">Тип возвращаемого значения `Task<IActionResult>` представляет текущую операцию с помощью результата типа `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="62981-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="62981-339">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="62981-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="62981-340">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="62981-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="62981-341">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="62981-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="62981-342">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="62981-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="62981-343">При написании асинхронного кода, который использует EF, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="62981-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="62981-344">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="62981-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="62981-345">К ним относятся, например, `ToListAsync`, `SingleOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="62981-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="62981-346">В их число не входят, например, инструкции, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="62981-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="62981-347">Контекст EF не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="62981-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="62981-348">При вызове любого асинхронного метода EF всегда используйте ключевое слово `await`.</span><span class="sxs-lookup"><span data-stu-id="62981-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="62981-349">Чтобы воспользоваться преимуществами в производительности, которые обеспечивает асинхронный код, убедитесь, что все используемые пакеты библиотек также используют асинхронный код при вызове любых методов EF, выполняющих запросы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="62981-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="62981-350">Дополнительные сведения об асинхронных методах программирования в .NET см. в разделе [Обзор асинхронной модели](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="62981-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="62981-351">Ограничение полученных сущностей</span><span class="sxs-lookup"><span data-stu-id="62981-351">Limit entities fetched</span></span>

<span data-ttu-id="62981-352">Сведения об ограничении числа сущностей, возвращаемых в запросе, см. в статье [Важные замечания о производительности](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="62981-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="62981-353">В следующем учебнике описано, как выполнять основные операции CRUD (создание, чтение, обновление и удаление).</span><span class="sxs-lookup"><span data-stu-id="62981-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="62981-354">Реализация базовых функций CRUD</span><span class="sxs-lookup"><span data-stu-id="62981-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="62981-355">На примере веб-приложения Contoso University показано, как создавать веб-приложения MVC ASP.NET Core 2.2 с помощью Entity Framework (EF) Core 2.2 и Visual Studio 2017 или 2019.</span><span class="sxs-lookup"><span data-stu-id="62981-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="62981-356">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="62981-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="62981-357">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="62981-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="62981-358">Это первый учебник из серии, в котором с самого начала описывается построение примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="62981-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62981-359">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="62981-359">Prerequisites</span></span>

* [<span data-ttu-id="62981-360">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="62981-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="62981-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с указанными ниже рабочими нагрузками.</span><span class="sxs-lookup"><span data-stu-id="62981-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="62981-362">Рабочая нагрузка **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="62981-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="62981-363">Рабочая нагрузка **Кроссплатформенная разработка .NET Core**</span><span class="sxs-lookup"><span data-stu-id="62981-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="62981-364">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="62981-364">Troubleshooting</span></span>

<span data-ttu-id="62981-365">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="62981-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="62981-366">Список распространенных ошибок и способы их устранения см. в [разделе "Устранение неполадок" последнего руководства серии](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="62981-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="62981-367">Если вам не удалось найти нужную информацию, вы можете задать вопрос на сайте StackOverflow.com в разделах, посвященных [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="62981-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="62981-368">Эта серия включает в себя 10 учебников, содержание каждого из которых базируется на предыдущих учебниках.</span><span class="sxs-lookup"><span data-stu-id="62981-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="62981-369">После успешного завершения каждого руководства рекомендуется сохранять копию проекта.</span><span class="sxs-lookup"><span data-stu-id="62981-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="62981-370">Таким образом, при возникновении проблем вы сможете вернуться к предыдущему учебнику, а не к началу серии.</span><span class="sxs-lookup"><span data-stu-id="62981-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="62981-371">Веб-приложение Contoso University</span><span class="sxs-lookup"><span data-stu-id="62981-371">Contoso University web app</span></span>

<span data-ttu-id="62981-372">В рамках этих учебников вы будете создавать приложение, которое представляет собой простой веб-сайт университета.</span><span class="sxs-lookup"><span data-stu-id="62981-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="62981-373">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="62981-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="62981-374">Будет создано несколько экранов.</span><span class="sxs-lookup"><span data-stu-id="62981-374">Here are a few of the screens you'll create.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="62981-377">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="62981-377">Create web app</span></span>

* <span data-ttu-id="62981-378">Запустите Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62981-378">Open Visual Studio.</span></span>

* <span data-ttu-id="62981-379">В меню **Файл** выберите пункт **Создать > Проект**.</span><span class="sxs-lookup"><span data-stu-id="62981-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="62981-380">В области слева выберите **Установленные > Visual C# > Интернет**.</span><span class="sxs-lookup"><span data-stu-id="62981-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="62981-381">Выберите шаблон проекта **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="62981-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="62981-382">Введите имя **ContosoUniversity** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="62981-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Диалоговое окно создания нового проекта](intro/_static/new-project2.png)

* <span data-ttu-id="62981-384">Дождитесь появления диалогового окна **Создание веб-приложения ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="62981-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="62981-385">Выберите **.NET Core** , **ASP.NET Core 2.2** и шаблон **Веб-приложение (модель — представление — контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="62981-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="62981-386">Убедитесь, что для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="62981-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="62981-387">Нажмите кнопку **ОК**</span><span class="sxs-lookup"><span data-stu-id="62981-387">Select **OK**</span></span>

  ![Диалоговое окно "Создание проекта ASP.NET Core"](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="62981-389">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="62981-389">Set up the site style</span></span>

<span data-ttu-id="62981-390">Выполните незначительную настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="62981-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="62981-391">Откройте файл *Views/Shared/_Layout.cshtml* и внесите следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="62981-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="62981-392">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="62981-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="62981-393">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="62981-393">There are three occurrences.</span></span>

* <span data-ttu-id="62981-394">Добавьте пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="62981-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="62981-395">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="62981-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="62981-396">Замените содержимое файла *Views/Home/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="62981-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="62981-397">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="62981-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="62981-398">Откроется домашняя страница, на которой будут представлены созданные в рамках этих учебников вкладки.</span><span class="sxs-lookup"><span data-stu-id="62981-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Домашняя страница университета Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="62981-400">Сведения о пакетах NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="62981-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="62981-401">Чтобы реализовать поддержку EF Core в проекте, установите поставщик целевой базы данных.</span><span class="sxs-lookup"><span data-stu-id="62981-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="62981-402">В этом учебнике используется SQL Server, для которого требуется пакет поставщика [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="62981-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="62981-403">Этот пакет входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), поэтому ссылаться на него не нужно.</span><span class="sxs-lookup"><span data-stu-id="62981-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="62981-404">Этот пакет EF SQL Server и его зависимости (`Microsoft.EntityFrameworkCore` и `Microsoft.EntityFrameworkCore.Relational`) обеспечивают поддержку среды выполнения для платформы EF.</span><span class="sxs-lookup"><span data-stu-id="62981-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="62981-405">Пакет средств будет добавлен позднее в рамках учебника [Миграции](migrations.md).</span><span class="sxs-lookup"><span data-stu-id="62981-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="62981-406">Дополнительные сведения о других поставщиках баз данных, которые доступны для платформы Entity Framework Core, см. в разделе [Поставщики баз данных](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="62981-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="62981-407">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="62981-407">Create the data model</span></span>

<span data-ttu-id="62981-408">Теперь необходимо создать классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="62981-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="62981-409">Для начала создаются следующие три сущности.</span><span class="sxs-lookup"><span data-stu-id="62981-409">You'll start with the following three entities.</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="62981-411">Между сущностями `Student` и `Enrollment`, а также между сущностями `Course` и `Enrollment` существует отношение "один ко многим".</span><span class="sxs-lookup"><span data-stu-id="62981-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="62981-412">Другими словами, учащийся может быть зарегистрирован в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="62981-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="62981-413">В следующих разделах создаются классы для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="62981-414">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="62981-414">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="62981-416">В папке *Models* создайте файл класса с именем *Student.cs* и замените код шаблона следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="62981-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="62981-417">Свойство `ID` будет использоваться в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="62981-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="62981-418">По умолчанию платформа Entity Framework интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="62981-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="62981-419">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="62981-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="62981-420">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="62981-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="62981-421">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="62981-422">Другими словами, если со строкой `Student` в базе данных связаны две строки `Enrollment` (строки, в столбце внешнего ключа StudentID которых содержится первичный ключ этого учащегося), в этой сущности `Student` свойство навигации `Enrollments` будет содержать две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="62981-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="62981-423">Если свойство навигации может содержать несколько сущностей (как в отношениях "многие ко многим" или "один ко многим"), оно должно иметь тип списка, допускающий добавление, удаление и обновление записей, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="62981-424">Вы можете указать тип `ICollection<T>` либо, например, тип `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="62981-425">Если указан тип `ICollection<T>`, платформа EF по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="62981-426">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="62981-426">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="62981-428">В папке *Models* создайте файл *Enrollment.cs* и замените существующий код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="62981-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="62981-429">Свойство `EnrollmentID` будет использоваться в качестве первичного ключа. В этой сущности используется шаблон `classnameID` вместо `ID`, как в сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="62981-430">Как правило, следует выбирать один шаблон, который будет использоваться в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="62981-431">В этом случае демонстрируется возможность использования любого из шаблонов.</span><span class="sxs-lookup"><span data-stu-id="62981-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="62981-432">В [одном из следующих учебников](inheritance.md) вы узнаете, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="62981-433">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="62981-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="62981-434">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="62981-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="62981-435">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="62981-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="62981-436">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="62981-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="62981-437">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому это свойство может содержать одну сущность `Student` (в отличие от представленного ранее свойства навигации `Student.Enrollments`, которое может содержать несколько сущностей `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="62981-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="62981-438">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="62981-439">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="62981-440">Платформа Entity Framework интерпретирует свойство как свойство внешнего ключа, если оно имеет имя `<navigation property name><primary key property name>` (например, `StudentID` для свойства навигации `Student`, поскольку сущность `Student` имеет первичный ключ `ID`).</span><span class="sxs-lookup"><span data-stu-id="62981-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="62981-441">Свойства внешнего ключа также могут называться просто `<primary key property name>` (например, `CourseID` поскольку сущность `Course` имеет первичный ключ `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="62981-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="62981-442">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="62981-442">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="62981-444">В папке *Models* создайте файл *Course.cs* и замените существующий код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="62981-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="62981-445">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="62981-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="62981-446">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="62981-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="62981-447">Более подробное описание атрибута `DatabaseGenerated` будет приведено в [одном из следующих учебников](complex-data-model.md) этой серии.</span><span class="sxs-lookup"><span data-stu-id="62981-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="62981-448">Фактически, этот атрибут позволяет ввести первичный ключ для курса, а не использовать базу данных, чтобы создать его.</span><span class="sxs-lookup"><span data-stu-id="62981-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="62981-449">Создание контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="62981-449">Create the database context</span></span>

<span data-ttu-id="62981-450">Контекст базы данных — это основной класс, который координирует функциональные возможности Entity Framework для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="62981-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="62981-451">Этот класс создается путем наследования от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="62981-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="62981-452">В коде указываются сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="62981-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="62981-453">Также вы можете настроить реакцию платформы Entity Framework на некоторые события.</span><span class="sxs-lookup"><span data-stu-id="62981-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="62981-454">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="62981-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="62981-455">В папке проекта создайте папку *Data*.</span><span class="sxs-lookup"><span data-stu-id="62981-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="62981-456">В папке *Data* создайте новый файл класса с именем *SchoolContext.cs* и замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="62981-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="62981-457">Этот код создает свойство `DbSet` для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="62981-458">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных, а сущность — строке в этой таблице.</span><span class="sxs-lookup"><span data-stu-id="62981-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62981-459">Вы можете опустить инструкции `DbSet<Enrollment>` и `DbSet<Course>`. Это не нарушит функциональность.</span><span class="sxs-lookup"><span data-stu-id="62981-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="62981-460">Платформа Entity Framework включает эти инструкции неявно, поскольку сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="62981-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="62981-461">При создании базы данных платформа EF создает таблицы с именами, соответствующими именам свойств в `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="62981-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="62981-462">Имена свойств для коллекций, как правило, задаются во множественном числе (например, Students вместо Student), однако единого мнения по поводу присвоения имен во множественном числе таблицам среди разработчиков не существует.</span><span class="sxs-lookup"><span data-stu-id="62981-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="62981-463">В этих учебниках вместо принятого по умолчанию способа таблицам в DbContext присваиваются имена в единственном числе.</span><span class="sxs-lookup"><span data-stu-id="62981-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="62981-464">Чтобы сделать это, добавьте выделенный ниже код после последнего свойства DbSet.</span><span class="sxs-lookup"><span data-stu-id="62981-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="62981-465">Выполните сборку проекта, чтобы проверить его на ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="62981-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="62981-466">Регистрация SchoolContext</span><span class="sxs-lookup"><span data-stu-id="62981-466">Register the SchoolContext</span></span>

<span data-ttu-id="62981-467">ASP.NET Core по умолчанию реализует технологию [внедрения зависимостей](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="62981-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="62981-468">С помощью внедрения зависимостей службы (например, контекст базы данных EF) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="62981-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="62981-469">Затем компоненты, которые используют эти службы (например, контроллеры MVC), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="62981-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="62981-470">Код конструктора контроллера, который получает экземпляр контекста, будет приведен позднее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="62981-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="62981-471">Чтобы зарегистрировать `SchoolContext` как службу, откройте файл *Startup.cs* и добавьте выделенные строки в метод `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62981-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="62981-472">Имя строки подключения передается в контекст путем вызова метода для объекта `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="62981-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="62981-473">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="62981-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="62981-474">Добавьте инструкции `using` для пространств имен `ContosoUniversity.Data` и `Microsoft.EntityFrameworkCore`, после чего выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="62981-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="62981-475">Откройте файл *appsettings.json* и добавьте строку подключения, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="62981-475">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="62981-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="62981-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="62981-477">Строка подключения указывает на базу данных SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="62981-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="62981-478">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в производственной среде.</span><span class="sxs-lookup"><span data-stu-id="62981-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="62981-479">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="62981-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="62981-480">По умолчанию база данных LocalDB создает файлы *.mdf* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="62981-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="62981-481">Инициализация базы данных с тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="62981-481">Initialize DB with test data</span></span>

<span data-ttu-id="62981-482">Платформа Entity Framework создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="62981-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="62981-483">В этом разделе вы напишете метод, который вызывается после создания базы данных и заполняет ее тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="62981-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="62981-484">Здесь будет использоваться метод `EnsureCreated` для автоматического создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="62981-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="62981-485">В [одном из следующих учебников](migrations.md) вы узнаете, как обрабатывать изменения модели с использованием Code First Migrations, что позволяет изменять схему базы данных вместо того, чтобы удалять и повторно создавать ее.</span><span class="sxs-lookup"><span data-stu-id="62981-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="62981-486">В папке *Data* создайте новый файл класса с именем *DbInitializer.cs* и замените код шаблона следующим кодом, который обеспечивает создание базы данных и загрузку в нее тестовых данных.</span><span class="sxs-lookup"><span data-stu-id="62981-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="62981-487">Этот код проверяет, добавлены ли в базу данных учащиеся. Если нет, база данных считается новой и заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="62981-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="62981-488">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="62981-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="62981-489">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее поведение при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="62981-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="62981-490">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="62981-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="62981-491">Вызов метода инициализации с передачей ему контекста.</span><span class="sxs-lookup"><span data-stu-id="62981-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="62981-492">Высвобождение контекста после завершения работы метода заполнения.</span><span class="sxs-lookup"><span data-stu-id="62981-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="62981-493">При первом запуске приложения будет создана и заполнена тестовыми данными необходимая для работы база данных.</span><span class="sxs-lookup"><span data-stu-id="62981-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="62981-494">При каждом изменении модели данных:</span><span class="sxs-lookup"><span data-stu-id="62981-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="62981-495">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="62981-495">Delete the database.</span></span>
 * <span data-ttu-id="62981-496">Обновите метод заполнения и запустите заново с новой базой данных таким же образом.</span><span class="sxs-lookup"><span data-stu-id="62981-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="62981-497">В следующих учебниках вы узнаете, как изменить базу данных при изменении модели данных, не прибегая к ее удалению и повторному созданию.</span><span class="sxs-lookup"><span data-stu-id="62981-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="62981-498">Создание контроллера и представлений</span><span class="sxs-lookup"><span data-stu-id="62981-498">Create controller and views</span></span>

<span data-ttu-id="62981-499">В этом разделе подсистема формирования шаблонов Visual Studio используется для добавления контроллера и представлений MVC, которые будут использовать платформу EF для запроса данных и их сохранения.</span><span class="sxs-lookup"><span data-stu-id="62981-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="62981-500">Автоматическое создание методов и представлений операций CRUD (создание, чтение, обновление и удаление) называется формированием шаблонов.</span><span class="sxs-lookup"><span data-stu-id="62981-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="62981-501">Формирование шаблонов отличается от создания кода тем, что шаблонный код является отправной точкой и может изменяться в соответствии с потребностями, тогда как сформированный код обычно не изменяется.</span><span class="sxs-lookup"><span data-stu-id="62981-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="62981-502">В тех случаях, когда требуется настроить созданный код в соответствии с внесенными изменениями, вы можете использовать разделяемые классы или повторно создать код.</span><span class="sxs-lookup"><span data-stu-id="62981-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="62981-503">Щелкните правой кнопкой мыши папку **Контроллеры** в **обозревателе решений** и выберите **Добавить > Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="62981-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="62981-504">В диалоговом окне **Добавление шаблона** :</span><span class="sxs-lookup"><span data-stu-id="62981-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="62981-505">Выберите **Контроллер MVC с представлениями, использующий Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="62981-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="62981-506">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="62981-506">Click **Add**.</span></span> <span data-ttu-id="62981-507">Откроется диалоговое окно **добавления контроллера MVC с представлениями с использованием Entity Framework**. ![Шаблон Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="62981-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="62981-508">В разделе **Класс модели** выберите **Student**.</span><span class="sxs-lookup"><span data-stu-id="62981-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="62981-509">В разделе **Класс контекста данных** выберите **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="62981-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="62981-510">Оставьте предлагаемое по умолчанию имя **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="62981-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="62981-511">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="62981-511">Click **Add**.</span></span>

<span data-ttu-id="62981-512">Подсистема формирования шаблонов Visual Studio создает файл *StudentsController.cs* и набор представлений (файлы с расширением *.cshtml* ), которые будут работать с контроллером.</span><span class="sxs-lookup"><span data-stu-id="62981-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="62981-513">Обратите внимание, что контроллер принимает `SchoolContext` в качестве параметра конструктора.</span><span class="sxs-lookup"><span data-stu-id="62981-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="62981-514">Технология внедрения зависимостей ASP.NET Core обеспечивает передачу экземпляра `SchoolContext` в контроллер.</span><span class="sxs-lookup"><span data-stu-id="62981-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="62981-515">Она настроена в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="62981-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="62981-516">Контроллер содержит метод действия `Index`, который отображает всех учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="62981-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="62981-517">Этот метод получает список учащихся из набора сущностей Students, считывая свойство `Students` экземпляра контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="62981-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="62981-518">Позднее в этом учебнике будут описаны элементы асинхронного программирования в этом коде.</span><span class="sxs-lookup"><span data-stu-id="62981-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="62981-519">Представление *Views/Students/Index.cshtml* отображает этот список в таблице:</span><span class="sxs-lookup"><span data-stu-id="62981-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="62981-520">Нажмите клавиши CTRL+F5, чтобы запустить проект, и выберите в меню **Отладка > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="62981-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="62981-521">Перейдите на вкладку Students (Учащиеся), чтобы просмотреть тестовые данные, добавленные методом `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="62981-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="62981-522">В зависимости от размеров окна браузера ссылку на вкладку `Students` можно найти вверху страницы или щелкнув значок навигации в правом верхнем углу.</span><span class="sxs-lookup"><span data-stu-id="62981-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Уменьшенное окно с домашней страницей университета Contoso](intro/_static/home-page-narrow.png)

![Страница указателя учащихся](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="62981-525">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="62981-525">View the database</span></span>

<span data-ttu-id="62981-526">При запуске приложения метод `DbInitializer.Initialize` вызывает метод `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="62981-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="62981-527">Платформа EF определяет, что база данных отсутствует, и создает ее, после чего код в оставшейся части метода `Initialize` заполняет базу данными.</span><span class="sxs-lookup"><span data-stu-id="62981-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="62981-528">Для просмотра базы данных в Visual Studio можно использовать **обозреватель объектов SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="62981-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="62981-529">Закройте браузер.</span><span class="sxs-lookup"><span data-stu-id="62981-529">Close the browser.</span></span>

<span data-ttu-id="62981-530">Если окно SSOX не открылось, выберите его в меню **Вид** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="62981-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="62981-531">В окне SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных** , а затем щелкните запись базы данных, имя которой указано в строке подключения в вашем файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="62981-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="62981-532">Разверните узел **Таблицы** , чтобы просмотреть представленные в базе таблицы.</span><span class="sxs-lookup"><span data-stu-id="62981-532">Expand the **Tables** node to see the tables in the database.</span></span>

![Таблицы в SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="62981-534">Щелкните правой кнопкой мыши таблицу **Student** и выберите пункт **Просмотр данных** , чтобы просмотреть созданные столбцы и строки, вставленные в базу данных.</span><span class="sxs-lookup"><span data-stu-id="62981-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Таблица Student в SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="62981-536">Файлы базы данных с расширением *MDF* и *LDF* располагаются в папке *C:\Users\\\<username>* .</span><span class="sxs-lookup"><span data-stu-id="62981-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="62981-537">Поскольку вы вызываете метод `EnsureCreated` в методе инициализатора, который выполняется при запуске приложения, теперь вы можете внести изменения в класс `Student`, удалить базу данных и снова запустить приложение. После этого база данных будет создана повторно в соответствии с внесенными изменениями.</span><span class="sxs-lookup"><span data-stu-id="62981-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="62981-538">Например, при добавлении свойства `EmailAddress` в класс `Student` во вновь созданной таблице появится столбец `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="62981-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="62981-539">Соглашения</span><span class="sxs-lookup"><span data-stu-id="62981-539">Conventions</span></span>

<span data-ttu-id="62981-540">Чтобы платформа Entity Framework автоматически создавала полную базу данных на основе принятых соглашений и допущений, потребуется написать минимальный объем кода.</span><span class="sxs-lookup"><span data-stu-id="62981-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="62981-541">В качестве имен таблиц используются имена свойств `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="62981-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="62981-542">Для сущностей, на которые не ссылается свойство `DbSet`, в качестве имен таблиц используются имена классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="62981-543">В качестве имен столбцов используются имена свойств сущностей.</span><span class="sxs-lookup"><span data-stu-id="62981-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="62981-544">Свойства сущностей с именем ID или classnameID распознаются как свойства первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="62981-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="62981-545">Свойство интерпретируется как свойство внешнего ключа, если оно имеет имя *\<navigation property name>\<primary key property name>* (например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`).</span><span class="sxs-lookup"><span data-stu-id="62981-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="62981-546">Свойства внешнего ключа также могут называться просто *\<primary key property name>* (например `EnrollmentID`, поскольку сущность `Enrollment` имеет первичный ключ `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="62981-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="62981-547">Стандартное поведение можно переопределить.</span><span class="sxs-lookup"><span data-stu-id="62981-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="62981-548">Например, можно явно задать имена таблиц, как было показано ранее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="62981-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="62981-549">Также можно указать имена столбцов и задать любое свойство в качестве первичного или внешнего ключа, как будет показано в [одном из следующих учебников](complex-data-model.md) этой серии.</span><span class="sxs-lookup"><span data-stu-id="62981-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="62981-550">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="62981-550">Asynchronous code</span></span>

<span data-ttu-id="62981-551">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="62981-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="62981-552">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="62981-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="62981-553">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="62981-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="62981-554">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="62981-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="62981-555">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="62981-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="62981-556">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="62981-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="62981-557">Во время выполнения асинхронный код использует немного больше служебных ресурсов, однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="62981-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="62981-558">В следующем коде для асинхронного выполнения используются ключевое слово `async`, возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="62981-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="62981-559">Ключевое слово `async` указывает компилятору создавать обратные вызовы для частей тела метода и автоматически создавать возвращаемый объект `Task<IActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="62981-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="62981-560">Тип возвращаемого значения `Task<IActionResult>` представляет текущую операцию с помощью результата типа `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="62981-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="62981-561">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="62981-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="62981-562">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="62981-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="62981-563">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="62981-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="62981-564">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="62981-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="62981-565">При написании асинхронного кода, который использует Entity Framework, необходимо учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="62981-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="62981-566">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="62981-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="62981-567">К ним относятся, например, `ToListAsync`, `SingleOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="62981-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="62981-568">В их число не входят, например, инструкции, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="62981-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="62981-569">Контекст EF не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="62981-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="62981-570">При вызове любого асинхронного метода EF всегда используйте ключевое слово `await`.</span><span class="sxs-lookup"><span data-stu-id="62981-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="62981-571">Если вы хотите использовать преимущества в производительности, которые обеспечивает асинхронный код, убедитесь, что все используемые пакеты библиотек (например, для разбиения на страницы) также используют асинхронный код при вызове любых методов Entity Framework, выполняющих запросы к базе данных.</span><span class="sxs-lookup"><span data-stu-id="62981-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="62981-572">Дополнительные сведения об асинхронных методах программирования в .NET см. в разделе [Обзор асинхронной модели](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="62981-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="62981-573">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="62981-573">Next steps</span></span>

<span data-ttu-id="62981-574">В следующем учебнике описано, как выполнять основные операции CRUD (создание, чтение, обновление и удаление).</span><span class="sxs-lookup"><span data-stu-id="62981-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="62981-575">Реализация базовых функций CRUD</span><span class="sxs-lookup"><span data-stu-id="62981-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
