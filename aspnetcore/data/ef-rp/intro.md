---
title: 'Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8'
author: rick-anderson
description: Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
uid: data/ef-rp/intro
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424295"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="7e760-103">Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8</span><span class="sxs-lookup"><span data-stu-id="7e760-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="7e760-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="7e760-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e760-105">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="7e760-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7e760-106">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="7e760-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e760-107">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="7e760-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e760-108">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="7e760-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7e760-109">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="7e760-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7e760-110">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="7e760-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e760-111">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e760-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e760-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="7e760-112">Prerequisites</span></span>

* <span data-ttu-id="7e760-113">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="7e760-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7e760-116">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="7e760-116">Database engines</span></span>

<span data-ttu-id="7e760-117">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="7e760-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7e760-118">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="7e760-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7e760-119">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="7e760-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e760-120">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="7e760-120">Troubleshooting</span></span>

<span data-ttu-id="7e760-121">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7e760-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e760-122">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7e760-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7e760-123">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-123">The sample app</span></span>

<span data-ttu-id="7e760-124">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="7e760-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7e760-125">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="7e760-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e760-126">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-126">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="7e760-129">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="7e760-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7e760-130">Это руководство посвящено использованию EF Core с ASP.NET Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="7e760-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="7e760-131">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-133">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="7e760-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e760-134">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7e760-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7e760-135">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="7e760-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7e760-136">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="7e760-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7e760-137">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 5.0**, а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="7e760-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-139">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="7e760-140">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="7e760-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e760-141">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="7e760-141">Set up the site style</span></span>

<span data-ttu-id="7e760-142">Скопируйте следующий код и вставьте его в файл *Pages/Shared/_Layout.cshtml*: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="7e760-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="7e760-143">Файл макета задает заголовок, нижний колонтитул и меню для сайта.</span><span class="sxs-lookup"><span data-stu-id="7e760-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="7e760-144">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="7e760-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="7e760-145">Заменяет все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7e760-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e760-146">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="7e760-146">There are three occurrences.</span></span>
* <span data-ttu-id="7e760-147">Удаляются пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="7e760-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="7e760-148">Добавляются пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="7e760-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7e760-149">Замените содержимое файла *Pages/Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="7e760-150">Приведенный выше код заменяет текст об ASP.NET Core текстом об этом приложении.</span><span class="sxs-lookup"><span data-stu-id="7e760-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="7e760-151">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="7e760-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7e760-152">Модель данных</span><span class="sxs-lookup"><span data-stu-id="7e760-152">The data model</span></span>

<span data-ttu-id="7e760-153">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-153">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e760-155">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="7e760-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7e760-156">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="7e760-156">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="7e760-158">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="7e760-159">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7e760-160">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="7e760-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7e760-161">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e760-162">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7e760-163">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="7e760-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7e760-164">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e760-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e760-165">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="7e760-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7e760-166">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="7e760-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7e760-167">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7e760-168">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="7e760-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7e760-169">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="7e760-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7e760-170">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="7e760-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7e760-171">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7e760-172">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7e760-173">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7e760-174">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7e760-175">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="7e760-175">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e760-177">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7e760-178">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7e760-179">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="7e760-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7e760-180">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="7e760-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7e760-181">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7e760-182">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="7e760-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e760-183">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="7e760-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7e760-184">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="7e760-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e760-185">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e760-186">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7e760-187">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e760-188">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e760-189">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e760-190">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e760-191">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e760-192">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7e760-193">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="7e760-193">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="7e760-195">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7e760-196">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="7e760-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e760-197">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e760-198">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7e760-199">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="7e760-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7e760-200">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="7e760-200">Scaffold Student pages</span></span>

<span data-ttu-id="7e760-201">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="7e760-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7e760-202">Класс `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="7e760-203">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7e760-204">Он является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7e760-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="7e760-205">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-207">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7e760-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="7e760-208">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="7e760-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e760-209">В диалоговом окне **Добавление нового элемента шаблона** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="7e760-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="7e760-210">На левой вкладке выберите **Установленные > Общие > Страницы Razor** .</span><span class="sxs-lookup"><span data-stu-id="7e760-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="7e760-211">Выберите **Razor Pages на основе Entity Framework (CRUD)**  > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7e760-212">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7e760-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7e760-213">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7e760-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7e760-214">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="7e760-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="7e760-215">Измените имя контекста данных так, чтобы оно заканчивалось на `SchoolContext`, а не на `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="7e760-216">Новое имя контекста: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="7e760-217">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-217">Select **Add**.</span></span>

<span data-ttu-id="7e760-218">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="7e760-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-220">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="7e760-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="7e760-221">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="7e760-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7e760-222">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7e760-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7e760-223">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7e760-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7e760-224">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7e760-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="7e760-225">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="7e760-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7e760-226">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="7e760-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="7e760-227">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="7e760-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="7e760-228">Если предыдущий шаг завершился сбоем, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="7e760-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7e760-229">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7e760-229">The scaffolding process:</span></span>

* <span data-ttu-id="7e760-230">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="7e760-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7e760-231">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-232">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-233">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-234">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-235">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7e760-236">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7e760-237">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7e760-238">добавляется строка подключения к базе данных в файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7e760-239">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="7e760-239">Database connection string</span></span>

<span data-ttu-id="7e760-240">Средство формирования шаблонов создает строку подключения в файле *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-242">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7e760-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="7e760-243">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="7e760-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e760-244">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-246">Сократите строку подключения SQLite до *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="7e760-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7e760-247">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-247">Update the database context class</span></span>

<span data-ttu-id="7e760-248">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7e760-249">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7e760-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e760-250">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e760-251">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e760-252">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7e760-253">Приведенный выше код изменяет форму слова `DbSet<Student> Student` на множественную: `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="7e760-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="7e760-254">Чтобы код Razor Pages соответствовал новому имени `DBSet`, произведите глобальное изменение `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="7e760-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="7e760-255">на `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="7e760-255">to: `_context.Students.`</span></span>

<span data-ttu-id="7e760-256">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="7e760-256">There are 8 occurrences.</span></span>

<span data-ttu-id="7e760-257">Так как набор сущностей содержит несколько сущностей, многие разработчики предпочитают имена свойств `DBSet` во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="7e760-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="7e760-258">Выделенный код:</span><span class="sxs-lookup"><span data-stu-id="7e760-258">The highlighted code:</span></span>

* <span data-ttu-id="7e760-259">создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="7e760-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e760-260">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="7e760-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="7e760-261">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="7e760-262">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="7e760-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="7e760-263">Вызывает <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="7e760-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="7e760-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="7e760-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="7e760-265">вызывается, когда контекст `SchoolContext` был инициализирован, но прежде чем модель была заблокирована и использована для инициализации контекста;</span><span class="sxs-lookup"><span data-stu-id="7e760-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="7e760-266">является обязательным, так как далее в руководстве сущность `Student` будет содержать ссылки на другие сущности.</span><span class="sxs-lookup"><span data-stu-id="7e760-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="7e760-267">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="7e760-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7e760-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7e760-268">Startup.cs</span></span>

<span data-ttu-id="7e760-269">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7e760-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e760-270">С помощью внедрения зависимостей службы, например `SchoolContext`, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7e760-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="7e760-271">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="7e760-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e760-272">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e760-273">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="7e760-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-275">Следующие выделенные строки были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="7e760-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-277">Убедитесь в том, что код, добавленный средством формирования шаблонов, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="7e760-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7e760-278">Сведения об использовании рабочей базы данных см. в статье [Использование SQLite для среды разработки и SQL Server для рабочей среды](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="7e760-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="7e760-279">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7e760-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e760-280">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="7e760-281">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-281">Add the database exception filter</span></span>

<span data-ttu-id="7e760-282">Добавьте `AddDatabaseDeveloperPageExceptionFilter` в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="7e760-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="7e760-284">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="7e760-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="7e760-285">Чтобы добавить пакет NuGet, в PMC введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7e760-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="7e760-287">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="7e760-288">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7e760-289">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-289">Create the database</span></span>

<span data-ttu-id="7e760-290">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="7e760-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7e760-291">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="7e760-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7e760-292">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="7e760-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7e760-293">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7e760-294">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="7e760-294">Delete the database.</span></span> <span data-ttu-id="7e760-295">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="7e760-295">Any existing data is lost.</span></span>
* <span data-ttu-id="7e760-296">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="7e760-296">Change the data model.</span></span> <span data-ttu-id="7e760-297">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7e760-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7e760-298">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-298">Run the app.</span></span>
* <span data-ttu-id="7e760-299">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="7e760-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7e760-300">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="7e760-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7e760-301">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="7e760-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7e760-302">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="7e760-303">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7e760-304">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e760-305">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-305">Test the app</span></span>

* <span data-ttu-id="7e760-306">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-306">Run the app.</span></span>
* <span data-ttu-id="7e760-307">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7e760-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e760-308">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="7e760-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7e760-309">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-309">Seed the database</span></span>

<span data-ttu-id="7e760-310">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7e760-311">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="7e760-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7e760-312">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7e760-313">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7e760-314">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="7e760-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7e760-315">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7e760-316">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7e760-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-318">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7e760-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="7e760-319">Выберите `Y`, чтобы удалить базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-321">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7e760-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7e760-322">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-322">Restart the app.</span></span>
* <span data-ttu-id="7e760-323">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="7e760-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7e760-324">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-326">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7e760-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7e760-327">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7e760-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7e760-328">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="7e760-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7e760-329">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="7e760-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7e760-330">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="7e760-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7e760-331">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-333">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7e760-334">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7e760-335">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="7e760-335">Asynchronous code</span></span>

<span data-ttu-id="7e760-336">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e760-337">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e760-338">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e760-339">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="7e760-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e760-340">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="7e760-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e760-341">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="7e760-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7e760-342">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="7e760-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e760-343">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="7e760-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e760-344">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7e760-345">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="7e760-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e760-346">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="7e760-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e760-347">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7e760-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7e760-348">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="7e760-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7e760-349">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="7e760-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e760-350">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="7e760-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e760-351">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="7e760-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e760-352">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7e760-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e760-353">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="7e760-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e760-354">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="7e760-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7e760-355">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e760-356">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7e760-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e760-357">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="7e760-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e760-358">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7e760-359">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7e760-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="7e760-360">Вопросы производительности</span><span class="sxs-lookup"><span data-stu-id="7e760-360">Performance considerations</span></span>

<span data-ttu-id="7e760-361">Как правило, веб-страница не должна загружать произвольное число строк.</span><span class="sxs-lookup"><span data-stu-id="7e760-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="7e760-362">Запрос должен использовать разбиение на страницы или применять ограничение.</span><span class="sxs-lookup"><span data-stu-id="7e760-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="7e760-363">Например, предыдущий запрос может использовать `Take` для ограничения количества возвращаемых строк:</span><span class="sxs-lookup"><span data-stu-id="7e760-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7e760-364">Если в процессе перечисления большой таблицы в представлении возникло исключение базы данных, может быть возвращен ответ HTTP 200 с сообщением о частично сформированных данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="7e760-365">Значение <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> по умолчанию равно 1024.</span><span class="sxs-lookup"><span data-stu-id="7e760-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="7e760-366">Следующий код задает `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="7e760-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7e760-367">Разбиение на страницы рассматривается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7e760-368">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="7e760-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7e760-369">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="7e760-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7e760-370">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="7e760-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7e760-371">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="7e760-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e760-372">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="7e760-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e760-373">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="7e760-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7e760-374">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="7e760-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7e760-375">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="7e760-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e760-376">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e760-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e760-377">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="7e760-377">Prerequisites</span></span>

* <span data-ttu-id="7e760-378">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="7e760-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7e760-381">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="7e760-381">Database engines</span></span>

<span data-ttu-id="7e760-382">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="7e760-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7e760-383">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="7e760-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7e760-384">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="7e760-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e760-385">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="7e760-385">Troubleshooting</span></span>

<span data-ttu-id="7e760-386">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7e760-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e760-387">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7e760-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7e760-388">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-388">The sample app</span></span>

<span data-ttu-id="7e760-389">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="7e760-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7e760-390">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="7e760-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e760-391">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-391">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="7e760-394">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="7e760-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7e760-395">Это руководство посвящено использованию EF Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="7e760-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="7e760-396">Чтобы получить исходный код готового проекта, перейдите по ссылке в верхней части страницы.</span><span class="sxs-lookup"><span data-stu-id="7e760-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="7e760-397">В папке *cu30* содержится код для версии учебника по ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7e760-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="7e760-398">Файлы, отражающие состояние кода для учебников 1–7, находятся в папке *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="7e760-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-400">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="7e760-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7e760-401">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-401">Build the project.</span></span>
* <span data-ttu-id="7e760-402">В консоли диспетчера пакетов (PMC) выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7e760-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="7e760-403">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-405">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="7e760-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7e760-406">Удалите файл *ContosoUniversity.csproj*, а файл *ContosoUniversitySQLite.csproj* переименуйте в *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7e760-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="7e760-407">В файле *Program.cs* закомментируйте `#define Startup`, чтобы использовать `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="7e760-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="7e760-408">Удалите файл *appSettings.json*, а файл *appSettingsSQLite.json* переименуйте в *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="7e760-409">Удалите папку *Migrations*, а папку *MigrationsSQL* переименуйте в *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="7e760-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="7e760-410">Выполните глобальный поиск `#if SQLiteVersion` и удалите `#if SQLiteVersion` и связанную инструкцию `#endif`.</span><span class="sxs-lookup"><span data-stu-id="7e760-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="7e760-411">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-411">Build the project.</span></span>
* <span data-ttu-id="7e760-412">В командной строке в папке проекта выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="7e760-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="7e760-413">В средстве SQLite выполните следующую инструкцию SQL:</span><span class="sxs-lookup"><span data-stu-id="7e760-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="7e760-414">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="7e760-415">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-417">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="7e760-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e760-418">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7e760-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7e760-419">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="7e760-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7e760-420">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="7e760-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7e760-421">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 3.0**, а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="7e760-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-423">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="7e760-424">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="7e760-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e760-425">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="7e760-425">Set up the site style</span></span>

<span data-ttu-id="7e760-426">Настройте заголовок сайта, нижний колонтитул и меню, внеся изменения в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7e760-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="7e760-427">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7e760-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e760-428">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="7e760-428">There are three occurrences.</span></span>

* <span data-ttu-id="7e760-429">Удалите пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность). Добавьте пункты **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="7e760-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7e760-430">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="7e760-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="7e760-431">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст об ASP.NET Core описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="7e760-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="7e760-432">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="7e760-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7e760-433">Модель данных</span><span class="sxs-lookup"><span data-stu-id="7e760-433">The data model</span></span>

<span data-ttu-id="7e760-434">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-434">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e760-436">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="7e760-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7e760-437">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="7e760-437">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="7e760-439">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="7e760-440">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7e760-441">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="7e760-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7e760-442">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e760-443">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7e760-444">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="7e760-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7e760-445">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e760-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e760-446">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="7e760-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7e760-447">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="7e760-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7e760-448">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7e760-449">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="7e760-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7e760-450">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="7e760-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7e760-451">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="7e760-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7e760-452">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7e760-453">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7e760-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7e760-454">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7e760-455">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7e760-456">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="7e760-456">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e760-458">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7e760-459">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7e760-460">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="7e760-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7e760-461">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="7e760-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7e760-462">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7e760-463">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="7e760-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e760-464">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="7e760-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7e760-465">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="7e760-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e760-466">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e760-467">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7e760-468">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e760-469">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e760-470">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e760-471">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e760-472">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e760-473">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7e760-474">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="7e760-474">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="7e760-476">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7e760-477">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="7e760-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e760-478">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e760-479">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7e760-480">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="7e760-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7e760-481">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="7e760-481">Scaffold Student pages</span></span>

<span data-ttu-id="7e760-482">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="7e760-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7e760-483">Класс *контекста* EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-483">An EF Core *context* class.</span></span> <span data-ttu-id="7e760-484">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7e760-485">Он является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="7e760-486">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-488">В папке *Pages* создайте папку *Students*.</span><span class="sxs-lookup"><span data-stu-id="7e760-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="7e760-489">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="7e760-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e760-490">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7e760-491">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7e760-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7e760-492">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7e760-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7e760-493">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="7e760-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="7e760-494">Измените имя контекста данных с *ContosoUniversity.Models.ContosoUniversityContext* на *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="7e760-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="7e760-495">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-495">Select **Add**.</span></span>

<span data-ttu-id="7e760-496">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="7e760-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-498">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="7e760-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="7e760-499">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="7e760-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7e760-500">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7e760-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7e760-501">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7e760-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7e760-502">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7e760-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="7e760-503">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="7e760-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7e760-504">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="7e760-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="7e760-505">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="7e760-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="7e760-506">Если в предыдущем шаге возникает проблема, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="7e760-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7e760-507">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7e760-507">The scaffolding process:</span></span>

* <span data-ttu-id="7e760-508">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="7e760-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7e760-509">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-510">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-511">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-512">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7e760-513">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7e760-514">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7e760-515">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="7e760-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7e760-516">добавляется строка подключения к базе данных в файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7e760-517">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="7e760-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-519">В файле *appsettings.json* указывается строка подключения для [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7e760-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="7e760-520">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="7e760-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e760-521">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-523">Измените строку подключения так, чтобы она указывала на файл базы данных SQLite с именем *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7e760-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7e760-524">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-524">Update the database context class</span></span>

<span data-ttu-id="7e760-525">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7e760-526">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7e760-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e760-527">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e760-528">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e760-529">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7e760-530">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="7e760-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e760-531">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="7e760-531">In EF Core terminology:</span></span>

* <span data-ttu-id="7e760-532">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="7e760-533">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="7e760-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7e760-534">Так как набор сущностей содержит несколько сущностей, свойства DBSet должны иметь имена во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="7e760-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="7e760-535">Так как средство формирования шаблонов создало DBSet `Student`, в этом шаге его имя меняется на имя во множественном числе: `Students`.</span><span class="sxs-lookup"><span data-stu-id="7e760-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="7e760-536">Чтобы код Razor Pages соответствовал новому имени DBSet, измените `_context.Student` на `_context.Students` во всем проекте.</span><span class="sxs-lookup"><span data-stu-id="7e760-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="7e760-537">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="7e760-537">There are 8 occurrences.</span></span>

<span data-ttu-id="7e760-538">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="7e760-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7e760-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7e760-539">Startup.cs</span></span>

<span data-ttu-id="7e760-540">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7e760-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e760-541">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7e760-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7e760-542">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="7e760-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e760-543">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e760-544">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="7e760-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-546">Выделенные строки в `ConfigureServices` были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="7e760-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-548">Убедитесь в том, что код, добавленный средством формирования шаблонов в `ConfigureServices`, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="7e760-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="7e760-549">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7e760-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e760-550">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7e760-551">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-551">Create the database</span></span>

<span data-ttu-id="7e760-552">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="7e760-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7e760-553">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="7e760-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7e760-554">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="7e760-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7e760-555">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7e760-556">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="7e760-556">Delete the database.</span></span> <span data-ttu-id="7e760-557">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="7e760-557">Any existing data is lost.</span></span>
* <span data-ttu-id="7e760-558">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="7e760-558">Change the data model.</span></span> <span data-ttu-id="7e760-559">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7e760-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7e760-560">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-560">Run the app.</span></span>
* <span data-ttu-id="7e760-561">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="7e760-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7e760-562">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="7e760-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7e760-563">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="7e760-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7e760-564">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="7e760-565">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7e760-566">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="7e760-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e760-567">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-567">Test the app</span></span>

* <span data-ttu-id="7e760-568">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-568">Run the app.</span></span>
* <span data-ttu-id="7e760-569">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7e760-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e760-570">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="7e760-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7e760-571">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-571">Seed the database</span></span>

<span data-ttu-id="7e760-572">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7e760-573">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="7e760-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7e760-574">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7e760-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7e760-575">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7e760-576">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="7e760-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7e760-577">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="7e760-578">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7e760-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-580">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7e760-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-582">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7e760-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7e760-583">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-583">Restart the app.</span></span>

* <span data-ttu-id="7e760-584">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="7e760-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7e760-585">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-587">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7e760-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7e760-588">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7e760-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7e760-589">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="7e760-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7e760-590">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="7e760-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7e760-591">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="7e760-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7e760-592">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-594">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7e760-595">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7e760-596">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="7e760-596">Asynchronous code</span></span>

<span data-ttu-id="7e760-597">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e760-598">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e760-599">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e760-600">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="7e760-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e760-601">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="7e760-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e760-602">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="7e760-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7e760-603">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="7e760-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e760-604">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="7e760-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e760-605">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7e760-606">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="7e760-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e760-607">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="7e760-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e760-608">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7e760-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7e760-609">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="7e760-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7e760-610">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="7e760-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e760-611">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="7e760-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e760-612">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="7e760-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e760-613">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7e760-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e760-614">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="7e760-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e760-615">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="7e760-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7e760-616">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e760-617">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7e760-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e760-618">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="7e760-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e760-619">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7e760-620">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7e760-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7e760-621">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="7e760-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7e760-622">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="7e760-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e760-623">На примере веб-приложения Contoso University показано, как создать веб-приложение Razor Pages ASP.NET Core с помощью Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="7e760-624">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="7e760-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e760-625">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="7e760-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e760-626">Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="7e760-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="7e760-627">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="7e760-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e760-628">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e760-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e760-629">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="7e760-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="7e760-632">Опыт работы с [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="7e760-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="7e760-633">Начинающие программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.</span><span class="sxs-lookup"><span data-stu-id="7e760-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e760-634">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="7e760-634">Troubleshooting</span></span>

<span data-ttu-id="7e760-635">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7e760-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e760-636">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделе, посвященном [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7e760-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="7e760-637">Веб-приложение университета Contoso</span><span class="sxs-lookup"><span data-stu-id="7e760-637">The Contoso University web app</span></span>

<span data-ttu-id="7e760-638">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="7e760-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="7e760-639">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="7e760-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e760-640">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-640">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

<span data-ttu-id="7e760-643">Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="7e760-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="7e760-644">Это руководство посвящено использованию EF Core с Razor Pages, а не работе с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="7e760-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="7e760-645">Создание веб-приложения Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="7e760-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-647">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="7e760-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e760-648">Создайте новое веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="7e760-649">Назовите проект **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="7e760-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="7e760-650">Очень важно, чтобы проект имел имя *ContosoUniversity*, чтобы совпадали пространства имен при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="7e760-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="7e760-651">Выберите в раскрывающемся списке **ASP.NET Core 2.1**, а затем **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="7e760-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="7e760-652">Изображения для приведенных выше шагов см. в разделе [Создание веб-приложения Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="7e760-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="7e760-653">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e760-655">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="7e760-655">Set up the site style</span></span>

<span data-ttu-id="7e760-656">Выполните небольшую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="7e760-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="7e760-657">Внесите следующие изменения в файл *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7e760-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="7e760-658">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7e760-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e760-659">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="7e760-659">There are three occurrences.</span></span>

* <span data-ttu-id="7e760-660">Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).</span><span class="sxs-lookup"><span data-stu-id="7e760-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="7e760-661">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="7e760-661">The changes are highlighted.</span></span> <span data-ttu-id="7e760-662">(Вся разметка *не* отображается.)</span><span class="sxs-lookup"><span data-stu-id="7e760-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="7e760-663">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="7e760-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="7e760-664">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="7e760-664">Create the data model</span></span>

<span data-ttu-id="7e760-665">Создайте классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="7e760-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="7e760-666">Начните со следующих трех сущностей:</span><span class="sxs-lookup"><span data-stu-id="7e760-666">Start with the following three entities:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e760-668">Между сущностями `Student` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="7e760-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="7e760-669">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="7e760-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="7e760-670">Студент может записаться на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="7e760-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="7e760-671">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="7e760-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="7e760-672">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="7e760-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="7e760-673">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="7e760-673">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="7e760-675">Создайте папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e760-675">Create a *Models* folder.</span></span> <span data-ttu-id="7e760-676">В папке *Models* создайте файл класса с именем *Student.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="7e760-677">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="7e760-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="7e760-678">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e760-679">В `classnameID``classname` — это имя класса.</span><span class="sxs-lookup"><span data-stu-id="7e760-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="7e760-680">Альтернативным автоматически распознаваемым первичным ключом является `StudentID` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="7e760-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="7e760-681">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e760-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e760-682">Свойства навигации ссылаются на другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="7e760-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="7e760-683">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="7e760-684">Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="7e760-685">Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="7e760-686">Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="7e760-687">Таблица `Enrollment` содержит две строки с `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="7e760-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="7e760-688">`StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="7e760-689">Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="7e760-690">Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="7e760-691">Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="7e760-692">Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.</span><span class="sxs-lookup"><span data-stu-id="7e760-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="7e760-693">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="7e760-693">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e760-695">В папке *Models* создайте файл *Enrollment.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="7e760-696">Свойство `EnrollmentID` является первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="7e760-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="7e760-697">Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="7e760-698">Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="7e760-699">В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="7e760-700">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="7e760-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e760-701">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="7e760-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="7e760-702">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="7e760-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e760-703">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e760-704">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="7e760-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="7e760-705">Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="7e760-706">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e760-707">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e760-708">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e760-709">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e760-710">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e760-711">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7e760-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="7e760-712">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="7e760-712">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="7e760-714">В папке *Models* создайте файл *Course.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="7e760-715">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="7e760-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e760-716">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7e760-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e760-717">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="7e760-718">Формирование шаблона для модели Student</span><span class="sxs-lookup"><span data-stu-id="7e760-718">Scaffold the student model</span></span>

<span data-ttu-id="7e760-719">В этом разделе формируется шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="7e760-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="7e760-720">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели Student.</span><span class="sxs-lookup"><span data-stu-id="7e760-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="7e760-721">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="7e760-721">Build the project.</span></span>
* <span data-ttu-id="7e760-722">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7e760-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e760-724">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="7e760-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e760-725">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="7e760-726">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7e760-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7e760-727">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7e760-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="7e760-728">В строке **Класс контекста данных** щелкните значок плюса ( **+** ) и измените автоматически присвоенное имя на **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="7e760-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="7e760-729">В раскрывающемся списке **Класс контекста данных** выберите **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="7e760-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="7e760-730">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7e760-730">Select **Add**.</span></span>

![Диалоговое окно CRUD](intro/_static/s1.png)

<span data-ttu-id="7e760-732">Если на предыдущем шаге у вас возникли проблемы, см. раздел [Создание модели фильма](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="7e760-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e760-734">Выполните следующую команду, чтобы сформировать шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="7e760-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="7e760-735">В процессе формирования шаблонов были созданы и изменены следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="7e760-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7e760-736">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="7e760-736">Files created</span></span>

* <span data-ttu-id="7e760-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="7e760-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="7e760-738">*Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="7e760-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="7e760-739">Обновления файла</span><span class="sxs-lookup"><span data-stu-id="7e760-739">File updates</span></span>

* <span data-ttu-id="7e760-740">*Startup.cs*: изменения в этом файле подробно описываются в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="7e760-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="7e760-741">*appsettings.json*: добавляется строка подключения, используемая для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7e760-742">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="7e760-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7e760-743">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7e760-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e760-744">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7e760-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7e760-745">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="7e760-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e760-746">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="7e760-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e760-747">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="7e760-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7e760-748">Проверьте метод `ConfigureServices` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7e760-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="7e760-749">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="7e760-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="7e760-750">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7e760-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e760-751">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e760-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="7e760-752">Обновление метода Main</span><span class="sxs-lookup"><span data-stu-id="7e760-752">Update main</span></span>

<span data-ttu-id="7e760-753">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:</span><span class="sxs-lookup"><span data-stu-id="7e760-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7e760-754">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="7e760-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7e760-755">Вызовите [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="7e760-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="7e760-756">Высвободите контекст после завершения работы метода `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="7e760-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="7e760-757">В следующем примере кода показан обновленный файл *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="7e760-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="7e760-758">`EnsureCreated` позволяет проверить существование базы данных для контекста.</span><span class="sxs-lookup"><span data-stu-id="7e760-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="7e760-759">Если контекст существует, никаких действий не предпринимается.</span><span class="sxs-lookup"><span data-stu-id="7e760-759">If it exists, no action is taken.</span></span> <span data-ttu-id="7e760-760">Если контекст не существует, создаются база данных и вся ее схема.</span><span class="sxs-lookup"><span data-stu-id="7e760-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="7e760-761">`EnsureCreated` не использует миграции для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="7e760-762">Созданную с помощью `EnsureCreated` базу данных впоследствии нельзя обновить, используя миграции.</span><span class="sxs-lookup"><span data-stu-id="7e760-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="7e760-763">`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:</span><span class="sxs-lookup"><span data-stu-id="7e760-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="7e760-764">Удалите базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-764">Delete the DB.</span></span>
* <span data-ttu-id="7e760-765">Измените схему базы данных (например, добавьте поле `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="7e760-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="7e760-766">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7e760-766">Run the app.</span></span>
* <span data-ttu-id="7e760-767">`EnsureCreated` создает базу данных со столбцом `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7e760-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="7e760-768">`EnsureCreated` удобно использовать на ранних стадиях разработки, когда схема часто меняется.</span><span class="sxs-lookup"><span data-stu-id="7e760-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="7e760-769">Далее в этом учебнике база данных удаляется и используются миграции.</span><span class="sxs-lookup"><span data-stu-id="7e760-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e760-770">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="7e760-770">Test the app</span></span>

<span data-ttu-id="7e760-771">Запустите приложение и примите политику использования файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="7e760-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="7e760-772">Это приложение не хранит персональные данные.</span><span class="sxs-lookup"><span data-stu-id="7e760-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="7e760-773">Сведения о политике использования файлов cookie можно прочитать в разделе [Поддержка общего регламента по защите данных ЕС (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="7e760-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="7e760-774">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7e760-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e760-775">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="7e760-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="7e760-776">Проверка контекста базы данных SchoolContext</span><span class="sxs-lookup"><span data-stu-id="7e760-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="7e760-777">Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="7e760-778">Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7e760-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e760-779">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e760-780">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e760-781">Обновите файл *SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="7e760-782">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="7e760-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e760-783">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="7e760-783">In EF Core terminology:</span></span>

* <span data-ttu-id="7e760-784">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="7e760-785">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="7e760-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7e760-786">`DbSet<Enrollment>` и `DbSet<Course>` можно опустить.</span><span class="sxs-lookup"><span data-stu-id="7e760-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="7e760-787">Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="7e760-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="7e760-788">В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7e760-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="7e760-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7e760-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7e760-790">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7e760-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="7e760-791">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="7e760-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e760-792">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="7e760-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7e760-793">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="7e760-794">Добавление кода для инициализации базы данных с использованием тестовых данных</span><span class="sxs-lookup"><span data-stu-id="7e760-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="7e760-795">EF Core создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="7e760-796">В этом разделе создается метод `Initialize`, предназначенный для заполнения тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="7e760-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="7e760-797">В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="7e760-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="7e760-798">Примечание. Предыдущий код использует `Models` для пространства имен (`namespace ContosoUniversity.Models`) вместо `Data`.</span><span class="sxs-lookup"><span data-stu-id="7e760-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="7e760-799">`Models` согласуется с кодом, созданным средством формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="7e760-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="7e760-800">См. дополнительные сведения о [проблеме с формированием шаблонов на GitHub ](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="7e760-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="7e760-801">Код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="7e760-802">Если учащихся в базе данных нет, она заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="7e760-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="7e760-803">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="7e760-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7e760-804">Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="7e760-805">Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="7e760-806">В файле *Program.cs* измените метод `Main`, чтобы реализовать вызов `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7e760-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="7e760-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e760-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e760-808">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7e760-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e760-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e760-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e760-810">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7e760-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="7e760-811">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="7e760-811">View the DB</span></span>

<span data-ttu-id="7e760-812">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="7e760-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="7e760-813">Таким образом, имя базы данных будет SchoolContext-{GUID}.</span><span class="sxs-lookup"><span data-stu-id="7e760-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="7e760-814">GUID будет отличаться для каждого пользователя.</span><span class="sxs-lookup"><span data-stu-id="7e760-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="7e760-815">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7e760-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="7e760-816">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7e760-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="7e760-817">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="7e760-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="7e760-818">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="7e760-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="7e760-819">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="7e760-819">Asynchronous code</span></span>

<span data-ttu-id="7e760-820">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e760-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e760-821">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e760-822">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="7e760-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e760-823">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="7e760-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e760-824">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="7e760-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e760-825">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="7e760-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="7e760-826">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="7e760-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e760-827">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="7e760-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e760-828">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="7e760-829">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="7e760-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e760-830">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="7e760-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e760-831">автоматически создавать возвращаемый объект [Task](/dotnet/api/system.threading.tasks.task).</span><span class="sxs-lookup"><span data-stu-id="7e760-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="7e760-832">Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7e760-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="7e760-833">Неявный тип возвращаемого значения `Task` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="7e760-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="7e760-834">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="7e760-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e760-835">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="7e760-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e760-836">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="7e760-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e760-837">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7e760-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e760-838">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="7e760-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e760-839">Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="7e760-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="7e760-840">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7e760-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e760-841">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7e760-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e760-842">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="7e760-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e760-843">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7e760-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="7e760-844">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7e760-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="7e760-845">Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).</span><span class="sxs-lookup"><span data-stu-id="7e760-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="7e760-846">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7e760-846">Additional resources</span></span>

* [<span data-ttu-id="7e760-847">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="7e760-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="7e760-848">Вперед</span><span class="sxs-lookup"><span data-stu-id="7e760-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
