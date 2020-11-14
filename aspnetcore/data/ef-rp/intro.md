---
title: 'Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8'
author: rick-anderson
description: 'Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365422"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="36a77-103">Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8</span><span class="sxs-lookup"><span data-stu-id="36a77-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="36a77-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="36a77-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36a77-105">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="36a77-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="36a77-106">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="36a77-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="36a77-107">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="36a77-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="36a77-108">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="36a77-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="36a77-109">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="36a77-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="36a77-110">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="36a77-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="36a77-111">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="36a77-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36a77-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="36a77-112">Prerequisites</span></span>

* <span data-ttu-id="36a77-113">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="36a77-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="36a77-116">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="36a77-116">Database engines</span></span>

<span data-ttu-id="36a77-117">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="36a77-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="36a77-118">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="36a77-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="36a77-119">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="36a77-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="36a77-120">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="36a77-120">Troubleshooting</span></span>

<span data-ttu-id="36a77-121">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="36a77-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="36a77-122">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="36a77-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="36a77-123">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-123">The sample app</span></span>

<span data-ttu-id="36a77-124">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="36a77-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="36a77-125">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="36a77-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="36a77-126">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-126">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="36a77-129">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="36a77-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="36a77-130">Это руководство посвящено использованию EF Core с ASP.NET Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="36a77-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="36a77-131">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-133">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="36a77-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="36a77-134">Выберите **Веб-приложение ASP.NET Core**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="36a77-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="36a77-135">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="36a77-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="36a77-136">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="36a77-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="36a77-137">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="36a77-137">Select **Create**.</span></span>
* <span data-ttu-id="36a77-138">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 5.0** , а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="36a77-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-140">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="36a77-141">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="36a77-141">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="36a77-142">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="36a77-142">Set up the site style</span></span>

<span data-ttu-id="36a77-143">Скопируйте следующий код и вставьте его в файл *Pages/Shared/_Layout.cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="36a77-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="36a77-144">Файл макета задает заголовок, нижний колонтитул и меню для сайта.</span><span class="sxs-lookup"><span data-stu-id="36a77-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="36a77-145">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="36a77-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="36a77-146">Заменяет все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="36a77-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="36a77-147">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="36a77-147">There are three occurrences.</span></span>
* <span data-ttu-id="36a77-148">Удаляются пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="36a77-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="36a77-149">Добавляются пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="36a77-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="36a77-150">Замените содержимое файла *Pages/Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="36a77-151">Приведенный выше код заменяет текст об ASP.NET Core текстом об этом приложении.</span><span class="sxs-lookup"><span data-stu-id="36a77-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="36a77-152">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="36a77-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="36a77-153">Модель данных</span><span class="sxs-lookup"><span data-stu-id="36a77-153">The data model</span></span>

<span data-ttu-id="36a77-154">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-154">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="36a77-156">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="36a77-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="36a77-157">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="36a77-157">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="36a77-159">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="36a77-160">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="36a77-161">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="36a77-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="36a77-162">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="36a77-163">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="36a77-164">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="36a77-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="36a77-165">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="36a77-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="36a77-166">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="36a77-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="36a77-167">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="36a77-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="36a77-168">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="36a77-169">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="36a77-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="36a77-170">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="36a77-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="36a77-171">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="36a77-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="36a77-172">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="36a77-173">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="36a77-174">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="36a77-175">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="36a77-176">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="36a77-176">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="36a77-178">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="36a77-179">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="36a77-180">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="36a77-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="36a77-181">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="36a77-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="36a77-182">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="36a77-183">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="36a77-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="36a77-184">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="36a77-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="36a77-185">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="36a77-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="36a77-186">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="36a77-187">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="36a77-188">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="36a77-189">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="36a77-190">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="36a77-191">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="36a77-192">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="36a77-193">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="36a77-194">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="36a77-194">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="36a77-196">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="36a77-197">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="36a77-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="36a77-198">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="36a77-199">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="36a77-200">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="36a77-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="36a77-201">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="36a77-201">Scaffold Student pages</span></span>

<span data-ttu-id="36a77-202">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="36a77-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="36a77-203">Класс `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="36a77-204">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="36a77-205">Он является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="36a77-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="36a77-206">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-206">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-208">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="36a77-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="36a77-209">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="36a77-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="36a77-210">В диалоговом окне **Добавление нового элемента шаблона** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="36a77-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="36a77-211">На левой вкладке выберите **Установленные > Общие > Страницы Razor** .</span><span class="sxs-lookup"><span data-stu-id="36a77-211">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="36a77-212">Выберите **Razor Pages на основе Entity Framework (CRUD)**  > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-212">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="36a77-213">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="36a77-213">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="36a77-214">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="36a77-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="36a77-215">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="36a77-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="36a77-216">Измените имя контекста данных так, чтобы оно заканчивалось на `SchoolContext`, а не на `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="36a77-217">Новое имя контекста: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="36a77-218">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-218">Select **Add**.</span></span>

<span data-ttu-id="36a77-219">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="36a77-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-221">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="36a77-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="36a77-222">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="36a77-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="36a77-223">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="36a77-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="36a77-224">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="36a77-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="36a77-225">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="36a77-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="36a77-226">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="36a77-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="36a77-227">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="36a77-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="36a77-228">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="36a77-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="36a77-229">Если предыдущий шаг завершился сбоем, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="36a77-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="36a77-230">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="36a77-230">The scaffolding process:</span></span>

* <span data-ttu-id="36a77-231">создаются страницы Razor в папке *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="36a77-231">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="36a77-232">*Create.cshtml* и *Create.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-233">*Delete.cshtml* и *Delete.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-234">*Details.cshtml* и *Details.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-235">*Edit.cshtml* и *Edit.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-236">*Index.cshtml* и *Index.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="36a77-237">создается файл *Data/SchoolContext.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="36a77-238">добавляется контекст для внедрения зависимостей в файле *Startup.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="36a77-239">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-239">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="36a77-240">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="36a77-240">Database connection string</span></span>

<span data-ttu-id="36a77-241">Средство формирования шаблонов создает строку подключения в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-241">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-243">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="36a77-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="36a77-244">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="36a77-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="36a77-245">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-247">Сократите строку подключения SQLite до *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="36a77-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="36a77-248">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-248">Update the database context class</span></span>

<span data-ttu-id="36a77-249">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="36a77-250">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="36a77-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="36a77-251">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="36a77-252">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="36a77-253">Обновите файл *Data/SchoolContext.cs* , добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="36a77-254">Приведенный выше код изменяет форму слова `DbSet<Student> Student` на множественную: `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="36a77-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="36a77-255">Чтобы код Razor Pages соответствовал новому имени `DBSet`, произведите глобальное изменение `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="36a77-255">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="36a77-256">на `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="36a77-256">to: `_context.Students.`</span></span>

<span data-ttu-id="36a77-257">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="36a77-257">There are 8 occurrences.</span></span>

<span data-ttu-id="36a77-258">Так как набор сущностей содержит несколько сущностей, многие разработчики предпочитают имена свойств `DBSet` во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="36a77-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="36a77-259">Выделенный код:</span><span class="sxs-lookup"><span data-stu-id="36a77-259">The highlighted code:</span></span>

* <span data-ttu-id="36a77-260">создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="36a77-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="36a77-261">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="36a77-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="36a77-262">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="36a77-263">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="36a77-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="36a77-264">Вызывает <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="36a77-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="36a77-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="36a77-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="36a77-266">вызывается, когда контекст `SchoolContext` был инициализирован, но прежде чем модель была заблокирована и использована для инициализации контекста;</span><span class="sxs-lookup"><span data-stu-id="36a77-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="36a77-267">является обязательным, так как далее в руководстве сущность `Student` будет содержать ссылки на другие сущности.</span><span class="sxs-lookup"><span data-stu-id="36a77-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="36a77-268">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="36a77-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="36a77-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="36a77-269">Startup.cs</span></span>

<span data-ttu-id="36a77-270">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36a77-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="36a77-271">С помощью внедрения зависимостей службы, например `SchoolContext`, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="36a77-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="36a77-272">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="36a77-272">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="36a77-273">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="36a77-274">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="36a77-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-276">Следующие выделенные строки были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="36a77-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-278">Убедитесь в том, что код, добавленный средством формирования шаблонов, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="36a77-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="36a77-279">Сведения об использовании рабочей базы данных см. в статье [Использование SQLite для среды разработки и SQL Server для рабочей среды](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="36a77-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="36a77-280">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="36a77-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="36a77-281">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="36a77-282">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-282">Add the database exception filter</span></span>

<span data-ttu-id="36a77-283">Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="36a77-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="36a77-285">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="36a77-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="36a77-286">Чтобы добавить пакет NuGet, в PMC введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="36a77-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="36a77-288">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="36a77-289">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="36a77-290">`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="36a77-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="36a77-291">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-291">Create the database</span></span>

<span data-ttu-id="36a77-292">Обновите файл *Program.cs* , чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="36a77-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="36a77-293">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="36a77-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="36a77-294">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="36a77-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="36a77-295">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="36a77-296">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="36a77-296">Delete the database.</span></span> <span data-ttu-id="36a77-297">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="36a77-297">Any existing data is lost.</span></span>
* <span data-ttu-id="36a77-298">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="36a77-298">Change the data model.</span></span> <span data-ttu-id="36a77-299">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="36a77-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="36a77-300">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-300">Run the app.</span></span>
* <span data-ttu-id="36a77-301">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="36a77-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="36a77-302">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="36a77-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="36a77-303">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="36a77-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="36a77-304">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="36a77-305">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="36a77-306">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="36a77-307">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-307">Test the app</span></span>

* <span data-ttu-id="36a77-308">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-308">Run the app.</span></span>
* <span data-ttu-id="36a77-309">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="36a77-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="36a77-310">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="36a77-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="36a77-311">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-311">Seed the database</span></span>

<span data-ttu-id="36a77-312">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="36a77-313">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="36a77-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="36a77-314">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="36a77-315">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="36a77-316">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="36a77-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="36a77-317">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="36a77-318">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="36a77-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-320">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="36a77-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="36a77-321">Выберите `Y`, чтобы удалить базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-323">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="36a77-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="36a77-324">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-324">Restart the app.</span></span>
* <span data-ttu-id="36a77-325">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="36a77-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="36a77-326">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-328">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36a77-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="36a77-329">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="36a77-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="36a77-330">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="36a77-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="36a77-331">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="36a77-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="36a77-332">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных** , чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="36a77-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="36a77-333">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код** , чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-335">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="36a77-336">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="36a77-337">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="36a77-337">Asynchronous code</span></span>

<span data-ttu-id="36a77-338">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="36a77-339">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="36a77-340">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="36a77-341">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="36a77-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="36a77-342">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="36a77-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="36a77-343">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="36a77-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="36a77-344">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="36a77-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="36a77-345">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="36a77-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="36a77-346">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="36a77-347">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="36a77-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="36a77-348">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="36a77-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="36a77-349">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="36a77-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="36a77-350">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="36a77-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="36a77-351">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="36a77-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="36a77-352">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="36a77-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="36a77-353">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="36a77-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="36a77-354">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="36a77-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="36a77-355">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="36a77-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="36a77-356">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="36a77-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="36a77-357">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="36a77-358">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="36a77-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="36a77-359">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="36a77-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="36a77-360">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="36a77-361">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="36a77-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="36a77-362">Вопросы производительности</span><span class="sxs-lookup"><span data-stu-id="36a77-362">Performance considerations</span></span>

<span data-ttu-id="36a77-363">Как правило, веб-страница не должна загружать произвольное число строк.</span><span class="sxs-lookup"><span data-stu-id="36a77-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="36a77-364">Запрос должен использовать разбиение на страницы или применять ограничение.</span><span class="sxs-lookup"><span data-stu-id="36a77-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="36a77-365">Например, предыдущий запрос может использовать `Take` для ограничения количества возвращаемых строк:</span><span class="sxs-lookup"><span data-stu-id="36a77-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="36a77-366">Если в процессе перечисления большой таблицы в представлении возникло исключение базы данных, может быть возвращен ответ HTTP 200 с сообщением о частично сформированных данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="36a77-367">Значение <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> по умолчанию равно 1024.</span><span class="sxs-lookup"><span data-stu-id="36a77-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="36a77-368">Следующий код задает `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="36a77-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36a77-369">Разбиение на страницы рассматривается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="36a77-370">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="36a77-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="36a77-371">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="36a77-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="36a77-372">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="36a77-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="36a77-373">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="36a77-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="36a77-374">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="36a77-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="36a77-375">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="36a77-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="36a77-376">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="36a77-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="36a77-377">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="36a77-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="36a77-378">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="36a77-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36a77-379">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="36a77-379">Prerequisites</span></span>

* <span data-ttu-id="36a77-380">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="36a77-380">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="36a77-383">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="36a77-383">Database engines</span></span>

<span data-ttu-id="36a77-384">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="36a77-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="36a77-385">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="36a77-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="36a77-386">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="36a77-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="36a77-387">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="36a77-387">Troubleshooting</span></span>

<span data-ttu-id="36a77-388">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="36a77-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="36a77-389">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="36a77-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="36a77-390">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-390">The sample app</span></span>

<span data-ttu-id="36a77-391">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="36a77-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="36a77-392">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="36a77-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="36a77-393">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-393">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="36a77-396">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="36a77-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="36a77-397">Это руководство посвящено использованию EF Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="36a77-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="36a77-398">Чтобы получить исходный код готового проекта, перейдите по ссылке в верхней части страницы.</span><span class="sxs-lookup"><span data-stu-id="36a77-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="36a77-399">В папке *cu30* содержится код для версии учебника по ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="36a77-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="36a77-400">Файлы, отражающие состояние кода для учебников 1–7, находятся в папке *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="36a77-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-402">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="36a77-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="36a77-403">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-403">Build the project.</span></span>
* <span data-ttu-id="36a77-404">В консоли диспетчера пакетов (PMC) выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="36a77-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="36a77-405">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-407">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="36a77-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="36a77-408">Удалите файл *ContosoUniversity.csproj* , а файл *ContosoUniversitySQLite.csproj* переименуйте в *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="36a77-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="36a77-409">В файле *Program.cs* закомментируйте `#define Startup`, чтобы использовать `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="36a77-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="36a77-410">Удалите файл *appSettings.json* , а файл *appSettingsSQLite.json* переименуйте в *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36a77-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="36a77-411">Удалите папку *Migrations* , а папку *MigrationsSQL* переименуйте в *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="36a77-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="36a77-412">Выполните глобальный поиск `#if SQLiteVersion` и удалите `#if SQLiteVersion` и связанную инструкцию `#endif`.</span><span class="sxs-lookup"><span data-stu-id="36a77-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="36a77-413">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-413">Build the project.</span></span>
* <span data-ttu-id="36a77-414">В командной строке в папке проекта выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="36a77-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="36a77-415">В средстве SQLite выполните следующую инструкцию SQL:</span><span class="sxs-lookup"><span data-stu-id="36a77-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="36a77-416">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="36a77-417">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-419">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="36a77-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="36a77-420">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="36a77-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="36a77-421">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="36a77-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="36a77-422">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="36a77-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="36a77-423">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 3.0** , а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="36a77-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-425">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="36a77-426">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="36a77-426">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="36a77-427">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="36a77-427">Set up the site style</span></span>

<span data-ttu-id="36a77-428">Настройте заголовок сайта, нижний колонтитул и меню, внеся изменения в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36a77-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="36a77-429">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="36a77-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="36a77-430">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="36a77-430">There are three occurrences.</span></span>

* <span data-ttu-id="36a77-431">Удалите пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность). Добавьте пункты **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="36a77-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="36a77-432">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="36a77-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="36a77-433">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст об ASP.NET Core описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="36a77-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="36a77-434">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="36a77-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="36a77-435">Модель данных</span><span class="sxs-lookup"><span data-stu-id="36a77-435">The data model</span></span>

<span data-ttu-id="36a77-436">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-436">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="36a77-438">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="36a77-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="36a77-439">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="36a77-439">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="36a77-441">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="36a77-442">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="36a77-443">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="36a77-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="36a77-444">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="36a77-445">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="36a77-446">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="36a77-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="36a77-447">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="36a77-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="36a77-448">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="36a77-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="36a77-449">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="36a77-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="36a77-450">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="36a77-451">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="36a77-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="36a77-452">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="36a77-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="36a77-453">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="36a77-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="36a77-454">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="36a77-455">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="36a77-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="36a77-456">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="36a77-457">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="36a77-458">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="36a77-458">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="36a77-460">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="36a77-461">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="36a77-462">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="36a77-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="36a77-463">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="36a77-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="36a77-464">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="36a77-465">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="36a77-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="36a77-466">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="36a77-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="36a77-467">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="36a77-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="36a77-468">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="36a77-469">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="36a77-470">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="36a77-471">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="36a77-472">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="36a77-473">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="36a77-474">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="36a77-475">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="36a77-476">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="36a77-476">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="36a77-478">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="36a77-479">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="36a77-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="36a77-480">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="36a77-481">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="36a77-482">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="36a77-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="36a77-483">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="36a77-483">Scaffold Student pages</span></span>

<span data-ttu-id="36a77-484">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="36a77-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="36a77-485">Класс *контекста* EF Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-485">An EF Core *context* class.</span></span> <span data-ttu-id="36a77-486">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="36a77-487">Он является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="36a77-488">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-488">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-490">В папке *Pages* создайте папку *Students*.</span><span class="sxs-lookup"><span data-stu-id="36a77-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="36a77-491">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="36a77-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="36a77-492">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-492">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="36a77-493">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="36a77-493">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="36a77-494">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="36a77-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="36a77-495">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="36a77-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="36a77-496">Измените имя контекста данных с *ContosoUniversity.Models.ContosoUniversityContext* на *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="36a77-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="36a77-497">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-497">Select **Add**.</span></span>

<span data-ttu-id="36a77-498">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="36a77-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-500">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="36a77-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="36a77-501">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="36a77-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="36a77-502">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="36a77-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="36a77-503">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="36a77-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="36a77-504">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="36a77-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="36a77-505">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="36a77-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="36a77-506">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="36a77-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="36a77-507">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="36a77-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="36a77-508">Если в предыдущем шаге возникает проблема, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="36a77-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="36a77-509">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="36a77-509">The scaffolding process:</span></span>

* <span data-ttu-id="36a77-510">создаются страницы Razor в папке *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="36a77-510">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="36a77-511">*Create.cshtml* и *Create.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-512">*Delete.cshtml* и *Delete.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-513">*Details.cshtml* и *Details.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-514">*Edit.cshtml* и *Edit.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="36a77-515">*Index.cshtml* и *Index.cshtml.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="36a77-516">создается файл *Data/SchoolContext.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="36a77-517">добавляется контекст для внедрения зависимостей в файле *Startup.cs* ;</span><span class="sxs-lookup"><span data-stu-id="36a77-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="36a77-518">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-518">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="36a77-519">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="36a77-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-521">В файле *appsettings.json* указывается строка подключения для [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="36a77-521">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="36a77-522">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="36a77-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="36a77-523">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-525">Измените строку подключения так, чтобы она указывала на файл базы данных SQLite с именем *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="36a77-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="36a77-526">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-526">Update the database context class</span></span>

<span data-ttu-id="36a77-527">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="36a77-528">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="36a77-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="36a77-529">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="36a77-530">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="36a77-531">Обновите файл *Data/SchoolContext.cs* , добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="36a77-532">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="36a77-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="36a77-533">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="36a77-533">In EF Core terminology:</span></span>

* <span data-ttu-id="36a77-534">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="36a77-535">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="36a77-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="36a77-536">Так как набор сущностей содержит несколько сущностей, свойства DBSet должны иметь имена во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="36a77-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="36a77-537">Так как средство формирования шаблонов создало DBSet `Student`, в этом шаге его имя меняется на имя во множественном числе: `Students`.</span><span class="sxs-lookup"><span data-stu-id="36a77-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="36a77-538">Чтобы код Razor Pages соответствовал новому имени DBSet, измените `_context.Student` на `_context.Students` во всем проекте.</span><span class="sxs-lookup"><span data-stu-id="36a77-538">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="36a77-539">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="36a77-539">There are 8 occurrences.</span></span>

<span data-ttu-id="36a77-540">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="36a77-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="36a77-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="36a77-541">Startup.cs</span></span>

<span data-ttu-id="36a77-542">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36a77-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="36a77-543">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="36a77-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="36a77-544">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="36a77-544">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="36a77-545">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="36a77-546">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="36a77-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-548">Выделенные строки в `ConfigureServices` были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="36a77-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-550">Убедитесь в том, что код, добавленный средством формирования шаблонов в `ConfigureServices`, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="36a77-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="36a77-551">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="36a77-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="36a77-552">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="36a77-553">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-553">Create the database</span></span>

<span data-ttu-id="36a77-554">Обновите файл *Program.cs* , чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="36a77-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="36a77-555">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="36a77-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="36a77-556">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="36a77-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="36a77-557">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="36a77-558">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="36a77-558">Delete the database.</span></span> <span data-ttu-id="36a77-559">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="36a77-559">Any existing data is lost.</span></span>
* <span data-ttu-id="36a77-560">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="36a77-560">Change the data model.</span></span> <span data-ttu-id="36a77-561">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="36a77-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="36a77-562">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-562">Run the app.</span></span>
* <span data-ttu-id="36a77-563">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="36a77-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="36a77-564">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="36a77-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="36a77-565">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="36a77-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="36a77-566">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="36a77-567">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="36a77-568">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="36a77-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="36a77-569">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-569">Test the app</span></span>

* <span data-ttu-id="36a77-570">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-570">Run the app.</span></span>
* <span data-ttu-id="36a77-571">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="36a77-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="36a77-572">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="36a77-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="36a77-573">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-573">Seed the database</span></span>

<span data-ttu-id="36a77-574">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="36a77-575">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="36a77-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="36a77-576">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="36a77-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="36a77-577">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="36a77-578">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="36a77-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="36a77-579">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="36a77-580">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="36a77-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-582">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="36a77-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-584">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="36a77-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="36a77-585">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-585">Restart the app.</span></span>

* <span data-ttu-id="36a77-586">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="36a77-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="36a77-587">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-589">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36a77-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="36a77-590">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="36a77-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="36a77-591">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="36a77-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="36a77-592">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="36a77-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="36a77-593">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных** , чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="36a77-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="36a77-594">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код** , чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-596">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="36a77-597">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="36a77-598">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="36a77-598">Asynchronous code</span></span>

<span data-ttu-id="36a77-599">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="36a77-600">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="36a77-601">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="36a77-602">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="36a77-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="36a77-603">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="36a77-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="36a77-604">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="36a77-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="36a77-605">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="36a77-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="36a77-606">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="36a77-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="36a77-607">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="36a77-608">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="36a77-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="36a77-609">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="36a77-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="36a77-610">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="36a77-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="36a77-611">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="36a77-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="36a77-612">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="36a77-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="36a77-613">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="36a77-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="36a77-614">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="36a77-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="36a77-615">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="36a77-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="36a77-616">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="36a77-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="36a77-617">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="36a77-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="36a77-618">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="36a77-619">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="36a77-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="36a77-620">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="36a77-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="36a77-621">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="36a77-622">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="36a77-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="36a77-623">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="36a77-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="36a77-624">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="36a77-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36a77-625">На примере веб-приложения Contoso University показано, как создать веб-приложение Razor Pages ASP.NET Core с помощью Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="36a77-626">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="36a77-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="36a77-627">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="36a77-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="36a77-628">Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="36a77-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="36a77-629">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="36a77-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="36a77-630">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="36a77-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36a77-631">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="36a77-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="36a77-634">Опыт работы с [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="36a77-634">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="36a77-635">Начинающие программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.</span><span class="sxs-lookup"><span data-stu-id="36a77-635">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="36a77-636">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="36a77-636">Troubleshooting</span></span>

<span data-ttu-id="36a77-637">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="36a77-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="36a77-638">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделе, посвященном [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="36a77-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="36a77-639">Веб-приложение университета Contoso</span><span class="sxs-lookup"><span data-stu-id="36a77-639">The Contoso University web app</span></span>

<span data-ttu-id="36a77-640">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="36a77-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="36a77-641">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="36a77-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="36a77-642">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-642">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

<span data-ttu-id="36a77-645">Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="36a77-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="36a77-646">Это руководство посвящено использованию EF Core с Razor Pages, а не работе с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="36a77-646">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="36a77-647">Создание веб-приложения Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="36a77-647">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-649">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="36a77-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="36a77-650">Создайте новое веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="36a77-651">Назовите проект **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="36a77-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="36a77-652">Очень важно, чтобы проект имел имя *ContosoUniversity* , чтобы совпадали пространства имен при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="36a77-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="36a77-653">Выберите в раскрывающемся списке **ASP.NET Core 2.1** , а затем **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="36a77-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="36a77-654">Изображения для приведенных выше шагов см. в разделе [Создание веб-приложения Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="36a77-654">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="36a77-655">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="36a77-657">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="36a77-657">Set up the site style</span></span>

<span data-ttu-id="36a77-658">Выполните небольшую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="36a77-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="36a77-659">Внесите следующие изменения в файл *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="36a77-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="36a77-660">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="36a77-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="36a77-661">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="36a77-661">There are three occurrences.</span></span>

* <span data-ttu-id="36a77-662">Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).</span><span class="sxs-lookup"><span data-stu-id="36a77-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="36a77-663">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="36a77-663">The changes are highlighted.</span></span> <span data-ttu-id="36a77-664">(Вся разметка *не* отображается.)</span><span class="sxs-lookup"><span data-stu-id="36a77-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="36a77-665">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="36a77-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="36a77-666">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="36a77-666">Create the data model</span></span>

<span data-ttu-id="36a77-667">Создайте классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="36a77-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="36a77-668">Начните со следующих трех сущностей:</span><span class="sxs-lookup"><span data-stu-id="36a77-668">Start with the following three entities:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="36a77-670">Между сущностями `Student` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="36a77-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="36a77-671">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="36a77-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="36a77-672">Студент может записаться на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="36a77-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="36a77-673">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="36a77-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="36a77-674">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="36a77-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="36a77-675">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="36a77-675">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="36a77-677">Создайте папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="36a77-677">Create a *Models* folder.</span></span> <span data-ttu-id="36a77-678">В папке *Models* создайте файл класса с именем *Student.cs* , содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="36a77-679">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="36a77-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="36a77-680">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="36a77-681">В `classnameID``classname` — это имя класса.</span><span class="sxs-lookup"><span data-stu-id="36a77-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="36a77-682">Альтернативным автоматически распознаваемым первичным ключом является `StudentID` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="36a77-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="36a77-683">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="36a77-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="36a77-684">Свойства навигации ссылаются на другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="36a77-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="36a77-685">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="36a77-686">Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="36a77-687">Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="36a77-688">Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="36a77-689">Таблица `Enrollment` содержит две строки с `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="36a77-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="36a77-690">`StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="36a77-691">Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="36a77-692">Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="36a77-693">Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="36a77-694">Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.</span><span class="sxs-lookup"><span data-stu-id="36a77-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="36a77-695">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="36a77-695">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="36a77-697">В папке *Models* создайте файл *Enrollment.cs* , содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="36a77-698">Свойство `EnrollmentID` является первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="36a77-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="36a77-699">Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="36a77-700">Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="36a77-701">В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="36a77-702">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="36a77-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="36a77-703">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="36a77-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="36a77-704">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="36a77-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="36a77-705">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="36a77-706">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="36a77-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="36a77-707">Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="36a77-708">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="36a77-709">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="36a77-710">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="36a77-711">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="36a77-712">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="36a77-713">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="36a77-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="36a77-714">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="36a77-714">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="36a77-716">В папке *Models* создайте файл *Course.cs* , содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="36a77-717">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="36a77-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="36a77-718">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="36a77-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="36a77-719">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="36a77-720">Формирование шаблона для модели Student</span><span class="sxs-lookup"><span data-stu-id="36a77-720">Scaffold the student model</span></span>

<span data-ttu-id="36a77-721">В этом разделе формируется шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="36a77-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="36a77-722">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели Student.</span><span class="sxs-lookup"><span data-stu-id="36a77-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="36a77-723">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="36a77-723">Build the project.</span></span>
* <span data-ttu-id="36a77-724">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="36a77-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36a77-726">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="36a77-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="36a77-727">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-727">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="36a77-728">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="36a77-728">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="36a77-729">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="36a77-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="36a77-730">В строке **Класс контекста данных** щелкните значок плюса ( **+** ) и измените автоматически присвоенное имя на **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="36a77-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="36a77-731">В раскрывающемся списке **Класс контекста данных** выберите **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="36a77-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="36a77-732">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="36a77-732">Select **Add**.</span></span>

![Диалоговое окно CRUD](intro/_static/s1.png)

<span data-ttu-id="36a77-734">Если на предыдущем шаге у вас возникли проблемы, см. раздел [Создание модели фильма](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="36a77-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36a77-736">Выполните следующую команду, чтобы сформировать шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="36a77-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="36a77-737">В процессе формирования шаблонов были созданы и изменены следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="36a77-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="36a77-738">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="36a77-738">Files created</span></span>

* <span data-ttu-id="36a77-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="36a77-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="36a77-740">*Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="36a77-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="36a77-741">Обновления файла</span><span class="sxs-lookup"><span data-stu-id="36a77-741">File updates</span></span>

* <span data-ttu-id="36a77-742">*Startup.cs* : изменения в этом файле подробно описываются в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="36a77-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="36a77-743">*appsettings.json* : добавляется строка подключения, используемая для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-743">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="36a77-744">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="36a77-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="36a77-745">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36a77-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="36a77-746">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="36a77-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="36a77-747">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="36a77-747">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="36a77-748">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="36a77-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="36a77-749">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="36a77-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="36a77-750">Проверьте метод `ConfigureServices` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="36a77-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="36a77-751">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="36a77-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="36a77-752">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="36a77-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="36a77-753">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36a77-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="36a77-754">Обновление метода Main</span><span class="sxs-lookup"><span data-stu-id="36a77-754">Update main</span></span>

<span data-ttu-id="36a77-755">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:</span><span class="sxs-lookup"><span data-stu-id="36a77-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="36a77-756">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="36a77-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="36a77-757">Вызовите [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="36a77-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="36a77-758">Высвободите контекст после завершения работы метода `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="36a77-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="36a77-759">В следующем примере кода показан обновленный файл *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="36a77-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="36a77-760">`EnsureCreated` позволяет проверить существование базы данных для контекста.</span><span class="sxs-lookup"><span data-stu-id="36a77-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="36a77-761">Если контекст существует, никаких действий не предпринимается.</span><span class="sxs-lookup"><span data-stu-id="36a77-761">If it exists, no action is taken.</span></span> <span data-ttu-id="36a77-762">Если контекст не существует, создаются база данных и вся ее схема.</span><span class="sxs-lookup"><span data-stu-id="36a77-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="36a77-763">`EnsureCreated` не использует миграции для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="36a77-764">Созданную с помощью `EnsureCreated` базу данных впоследствии нельзя обновить, используя миграции.</span><span class="sxs-lookup"><span data-stu-id="36a77-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="36a77-765">`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:</span><span class="sxs-lookup"><span data-stu-id="36a77-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="36a77-766">Удалите базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-766">Delete the DB.</span></span>
* <span data-ttu-id="36a77-767">Измените схему базы данных (например, добавьте поле `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="36a77-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="36a77-768">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="36a77-768">Run the app.</span></span>
* <span data-ttu-id="36a77-769">`EnsureCreated` создает базу данных со столбцом `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="36a77-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="36a77-770">`EnsureCreated` удобно использовать на ранних стадиях разработки, когда схема часто меняется.</span><span class="sxs-lookup"><span data-stu-id="36a77-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="36a77-771">Далее в этом учебнике база данных удаляется и используются миграции.</span><span class="sxs-lookup"><span data-stu-id="36a77-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="36a77-772">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="36a77-772">Test the app</span></span>

<span data-ttu-id="36a77-773">Запустите приложение и примите политику использования файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="36a77-773">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="36a77-774">Это приложение не хранит персональные данные.</span><span class="sxs-lookup"><span data-stu-id="36a77-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="36a77-775">Сведения о политике использования файлов cookie можно прочитать в разделе [Поддержка общего регламента по защите данных ЕС (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="36a77-775">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="36a77-776">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="36a77-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="36a77-777">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="36a77-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="36a77-778">Проверка контекста базы данных SchoolContext</span><span class="sxs-lookup"><span data-stu-id="36a77-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="36a77-779">Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="36a77-780">Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="36a77-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="36a77-781">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="36a77-782">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="36a77-783">Обновите файл *SchoolContext.cs* , добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="36a77-784">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="36a77-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="36a77-785">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="36a77-785">In EF Core terminology:</span></span>

* <span data-ttu-id="36a77-786">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="36a77-787">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="36a77-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="36a77-788">`DbSet<Enrollment>` и `DbSet<Course>` можно опустить.</span><span class="sxs-lookup"><span data-stu-id="36a77-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="36a77-789">Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="36a77-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="36a77-790">В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="36a77-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="36a77-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="36a77-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="36a77-792">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="36a77-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="36a77-793">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="36a77-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="36a77-794">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="36a77-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="36a77-795">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="36a77-796">Добавление кода для инициализации базы данных с использованием тестовых данных</span><span class="sxs-lookup"><span data-stu-id="36a77-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="36a77-797">EF Core создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="36a77-798">В этом разделе создается метод `Initialize`, предназначенный для заполнения тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="36a77-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="36a77-799">В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="36a77-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="36a77-800">Примечание. Предыдущий код использует `Models` для пространства имен (`namespace ContosoUniversity.Models`) вместо `Data`.</span><span class="sxs-lookup"><span data-stu-id="36a77-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="36a77-801">`Models` согласуется с кодом, созданным средством формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="36a77-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="36a77-802">См. дополнительные сведения о [проблеме с формированием шаблонов на GitHub ](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="36a77-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="36a77-803">Код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="36a77-804">Если учащихся в базе данных нет, она заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="36a77-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="36a77-805">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="36a77-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="36a77-806">Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="36a77-807">Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="36a77-808">В файле *Program.cs* измените метод `Main`, чтобы реализовать вызов `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="36a77-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="36a77-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36a77-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36a77-810">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="36a77-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="36a77-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36a77-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36a77-812">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="36a77-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="36a77-813">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="36a77-813">View the DB</span></span>

<span data-ttu-id="36a77-814">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="36a77-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="36a77-815">Таким образом, имя базы данных будет SchoolContext-{GUID}.</span><span class="sxs-lookup"><span data-stu-id="36a77-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="36a77-816">GUID будет отличаться для каждого пользователя.</span><span class="sxs-lookup"><span data-stu-id="36a77-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="36a77-817">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36a77-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="36a77-818">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="36a77-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="36a77-819">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="36a77-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="36a77-820">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных** , чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="36a77-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="36a77-821">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="36a77-821">Asynchronous code</span></span>

<span data-ttu-id="36a77-822">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="36a77-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="36a77-823">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="36a77-824">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="36a77-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="36a77-825">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="36a77-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="36a77-826">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="36a77-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="36a77-827">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="36a77-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="36a77-828">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="36a77-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="36a77-829">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="36a77-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="36a77-830">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="36a77-831">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="36a77-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="36a77-832">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="36a77-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="36a77-833">автоматически создавать возвращаемый объект [Task](/dotnet/api/system.threading.tasks.task).</span><span class="sxs-lookup"><span data-stu-id="36a77-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="36a77-834">Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="36a77-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="36a77-835">Неявный тип возвращаемого значения `Task` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="36a77-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="36a77-836">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="36a77-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="36a77-837">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="36a77-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="36a77-838">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="36a77-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="36a77-839">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="36a77-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="36a77-840">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="36a77-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="36a77-841">Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="36a77-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="36a77-842">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="36a77-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="36a77-843">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="36a77-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="36a77-844">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="36a77-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="36a77-845">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="36a77-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="36a77-846">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="36a77-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="36a77-847">Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).</span><span class="sxs-lookup"><span data-stu-id="36a77-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="36a77-848">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="36a77-848">Additional resources</span></span>

* [<span data-ttu-id="36a77-849">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="36a77-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="36a77-850">Вперед</span><span class="sxs-lookup"><span data-stu-id="36a77-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
