---
title: 'Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8'
author: rick-anderson
description: Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
uid: data/ef-rp/intro
ms.openlocfilehash: 9dcb1c4a19e50a57f1a1918cfcf775b49fa89b11
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320152"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="1c7f0-103">Использование Razor Pages с Entity Framework Core в ASP.NET Core: руководство 1 из 8</span><span class="sxs-lookup"><span data-stu-id="1c7f0-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="1c7f0-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="1c7f0-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1c7f0-105">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1c7f0-106">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1c7f0-107">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1c7f0-108">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1c7f0-109">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1c7f0-110">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1c7f0-111">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c7f0-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1c7f0-112">Prerequisites</span></span>

* <span data-ttu-id="1c7f0-113">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1c7f0-116">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="1c7f0-116">Database engines</span></span>

<span data-ttu-id="1c7f0-117">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1c7f0-118">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1c7f0-119">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1c7f0-120">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="1c7f0-120">Troubleshooting</span></span>

<span data-ttu-id="1c7f0-121">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1c7f0-122">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1c7f0-123">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-123">The sample app</span></span>

<span data-ttu-id="1c7f0-124">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1c7f0-125">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1c7f0-126">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-126">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="1c7f0-129">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1c7f0-130">Это руководство посвящено использованию EF Core с ASP.NET Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="1c7f0-131">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1c7f0-133">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="1c7f0-134">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="1c7f0-135">В диалоговом окне **Настроить новый проект** введите `ContosoUniversity` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="1c7f0-136">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен (`namespace`) совпадали при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="1c7f0-137">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-137">Select **Create**.</span></span>
1. <span data-ttu-id="1c7f0-138">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="1c7f0-139">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="1c7f0-140">**Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="1c7f0-141">Нажмите кнопку **Создать**.
      ![Диалоговое окно создания проекта ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="1c7f0-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-143">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="1c7f0-144">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1c7f0-145">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="1c7f0-145">Set up the site style</span></span>

<span data-ttu-id="1c7f0-146">Скопируйте следующий код и вставьте его в файл *Pages/Shared/_Layout.cshtml*: </span><span class="sxs-lookup"><span data-stu-id="1c7f0-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="1c7f0-147">Файл макета задает заголовок, нижний колонтитул и меню для сайта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="1c7f0-148">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1c7f0-149">Заменяет все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="1c7f0-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1c7f0-150">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-150">There are three occurrences.</span></span>
* <span data-ttu-id="1c7f0-151">Удаляются пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="1c7f0-152">Добавляются пункты меню **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="1c7f0-153">Замените содержимое файла *Pages/Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="1c7f0-154">Приведенный выше код заменяет текст об ASP.NET Core текстом об этом приложении.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="1c7f0-155">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1c7f0-156">Модель данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-156">The data model</span></span>

<span data-ttu-id="1c7f0-157">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-157">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="1c7f0-159">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1c7f0-160">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-160">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="1c7f0-162">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="1c7f0-163">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1c7f0-164">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1c7f0-165">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1c7f0-166">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1c7f0-167">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="1c7f0-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1c7f0-168">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1c7f0-169">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1c7f0-170">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1c7f0-171">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1c7f0-172">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1c7f0-173">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1c7f0-174">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1c7f0-175">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1c7f0-176">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1c7f0-177">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1c7f0-178">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1c7f0-179">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="1c7f0-179">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="1c7f0-181">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1c7f0-182">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1c7f0-183">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1c7f0-184">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1c7f0-185">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1c7f0-186">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1c7f0-187">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1c7f0-188">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1c7f0-189">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1c7f0-190">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1c7f0-191">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1c7f0-192">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1c7f0-193">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1c7f0-194">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1c7f0-195">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1c7f0-196">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1c7f0-197">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="1c7f0-197">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="1c7f0-199">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1c7f0-200">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1c7f0-201">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1c7f0-202">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1c7f0-203">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1c7f0-204">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-204">Scaffold Student pages</span></span>

<span data-ttu-id="1c7f0-205">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1c7f0-206">Класс `DbContext` EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="1c7f0-207">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1c7f0-208">Он является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="1c7f0-209">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-211">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="1c7f0-212">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1c7f0-213">В диалоговом окне **Добавление нового элемента шаблона** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="1c7f0-214">На левой вкладке выберите **Установленные > Общие > Страницы Razor** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="1c7f0-215">Выберите **Razor Pages на основе Entity Framework (CRUD)**  > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1c7f0-216">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1c7f0-217">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1c7f0-218">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="1c7f0-219">Измените имя контекста данных так, чтобы оно заканчивалось на `SchoolContext`, а не на `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="1c7f0-220">Новое имя контекста: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="1c7f0-221">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-221">Select **Add**.</span></span>

<span data-ttu-id="1c7f0-222">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-224">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="1c7f0-225">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1c7f0-226">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1c7f0-227">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1c7f0-228">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="1c7f0-229">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1c7f0-230">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="1c7f0-231">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="1c7f0-232">Если предыдущий шаг завершился сбоем, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1c7f0-233">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-233">The scaffolding process:</span></span>

* <span data-ttu-id="1c7f0-234">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1c7f0-235">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-236">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-237">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-238">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-239">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1c7f0-240">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1c7f0-241">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1c7f0-242">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1c7f0-243">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-243">Database connection string</span></span>

<span data-ttu-id="1c7f0-244">Средство формирования шаблонов создает строку подключения в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-246">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="1c7f0-247">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1c7f0-248">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-250">Сократите строку подключения SQLite до *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1c7f0-251">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-251">Update the database context class</span></span>

<span data-ttu-id="1c7f0-252">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1c7f0-253">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1c7f0-254">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1c7f0-255">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1c7f0-256">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1c7f0-257">Приведенный выше код изменяет форму слова `DbSet<Student> Student` на множественную: `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="1c7f0-258">Чтобы код Razor Pages соответствовал новому имени `DBSet`, произведите глобальное изменение `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="1c7f0-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="1c7f0-259">на `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-259">to: `_context.Students.`</span></span>

<span data-ttu-id="1c7f0-260">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-260">There are 8 occurrences.</span></span>

<span data-ttu-id="1c7f0-261">Так как набор сущностей содержит несколько сущностей, многие разработчики предпочитают имена свойств `DBSet` во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="1c7f0-262">Выделенный код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-262">The highlighted code:</span></span>

* <span data-ttu-id="1c7f0-263">создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1c7f0-264">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="1c7f0-265">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="1c7f0-266">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="1c7f0-267">Вызывает <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="1c7f0-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="1c7f0-269">вызывается, когда контекст `SchoolContext` был инициализирован, но прежде чем модель была заблокирована и использована для инициализации контекста;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="1c7f0-270">является обязательным, так как далее в руководстве сущность `Student` будет содержать ссылки на другие сущности.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="1c7f0-271">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1c7f0-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1c7f0-272">Startup.cs</span></span>

<span data-ttu-id="1c7f0-273">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1c7f0-274">С помощью внедрения зависимостей службы, например `SchoolContext`, регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="1c7f0-275">Затем компоненты, которые используют эти службы, например Razor Pages, обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="1c7f0-276">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1c7f0-277">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-279">Следующие выделенные строки были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-281">Убедитесь в том, что код, добавленный средством формирования шаблонов, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1c7f0-282">Сведения об использовании рабочей базы данных см. в статье [Использование SQLite для среды разработки и SQL Server для рабочей среды](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="1c7f0-283">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1c7f0-284">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="1c7f0-285">Добавление фильтра исключений базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-285">Add the database exception filter</span></span>

<span data-ttu-id="1c7f0-286">Добавьте <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> в `ConfigureServices`, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="1c7f0-288">Добавьте пакет NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="1c7f0-289">Чтобы добавить пакет NuGet, в PMC введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="1c7f0-291">Пакет NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` предоставляет ПО промежуточного слоя ASP.NET Core для страниц ошибок Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="1c7f0-292">Это ПО промежуточного слоя помогает обнаруживать и диагностировать ошибки с помощью миграций Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="1c7f0-293">`AddDatabaseDeveloperPageExceptionFilter` предоставляет полезные сведения об ошибках в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1c7f0-294">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-294">Create the database</span></span>

<span data-ttu-id="1c7f0-295">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1c7f0-296">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1c7f0-297">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1c7f0-298">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1c7f0-299">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-299">Delete the database.</span></span> <span data-ttu-id="1c7f0-300">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-300">Any existing data is lost.</span></span>
* <span data-ttu-id="1c7f0-301">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-301">Change the data model.</span></span> <span data-ttu-id="1c7f0-302">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1c7f0-303">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-303">Run the app.</span></span>
* <span data-ttu-id="1c7f0-304">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1c7f0-305">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1c7f0-306">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1c7f0-307">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="1c7f0-308">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1c7f0-309">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1c7f0-310">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-310">Test the app</span></span>

* <span data-ttu-id="1c7f0-311">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-311">Run the app.</span></span>
* <span data-ttu-id="1c7f0-312">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1c7f0-313">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1c7f0-314">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-314">Seed the database</span></span>

<span data-ttu-id="1c7f0-315">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1c7f0-316">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1c7f0-317">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1c7f0-318">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1c7f0-319">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1c7f0-320">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1c7f0-321">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-323">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="1c7f0-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="1c7f0-324">Выберите `Y`, чтобы удалить базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-326">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1c7f0-327">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-327">Restart the app.</span></span>
* <span data-ttu-id="1c7f0-328">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1c7f0-329">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-331">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1c7f0-332">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1c7f0-333">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1c7f0-334">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1c7f0-335">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1c7f0-336">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-338">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1c7f0-339">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1c7f0-340">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="1c7f0-340">Asynchronous code</span></span>

<span data-ttu-id="1c7f0-341">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1c7f0-342">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1c7f0-343">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1c7f0-344">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1c7f0-345">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1c7f0-346">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1c7f0-347">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1c7f0-348">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1c7f0-349">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1c7f0-350">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1c7f0-351">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1c7f0-352">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1c7f0-353">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1c7f0-354">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1c7f0-355">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1c7f0-356">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1c7f0-357">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1c7f0-358">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1c7f0-359">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1c7f0-360">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1c7f0-361">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1c7f0-362">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1c7f0-363">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1c7f0-364">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="1c7f0-365">Вопросы производительности</span><span class="sxs-lookup"><span data-stu-id="1c7f0-365">Performance considerations</span></span>

<span data-ttu-id="1c7f0-366">Как правило, веб-страница не должна загружать произвольное число строк.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="1c7f0-367">Запрос должен использовать разбиение на страницы или применять ограничение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="1c7f0-368">Например, предыдущий запрос может использовать `Take` для ограничения количества возвращаемых строк:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1c7f0-369">Если в процессе перечисления большой таблицы в представлении возникло исключение базы данных, может быть возвращен ответ HTTP 200 с сообщением о частично сформированных данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="1c7f0-370">Значение <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> по умолчанию равно 1024.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="1c7f0-371">Следующий код задает `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="1c7f0-372">Разбиение на страницы рассматривается далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c7f0-373">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="1c7f0-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1c7f0-374">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="1c7f0-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1c7f0-375">Это первое руководство из серии, посвященной использованию Entity Framework (EF) Core в приложении [ASP.NET Core Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1c7f0-376">В учебниках создается веб-сайт для вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1c7f0-377">На сайте предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1c7f0-378">В этом руководстве используется подход Code First.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1c7f0-379">См. сведения о работе с этим руководством при использовании подхода Database First в [этой проблеме GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1c7f0-380">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1c7f0-381">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c7f0-382">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1c7f0-382">Prerequisites</span></span>

* <span data-ttu-id="1c7f0-383">Если у вас нет опыта работы с Razor Pages, ознакомьтесь с серией руководств [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к изучению этого руководства.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1c7f0-386">Ядра СУБД</span><span class="sxs-lookup"><span data-stu-id="1c7f0-386">Database engines</span></span>

<span data-ttu-id="1c7f0-387">В инструкциях для Visual Studio используется [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), версия SQL Server Express, которая работает только в Windows.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1c7f0-388">В инструкциях для Visual Studio Code используется [SQLite](https://www.sqlite.org/), кроссплатформенное ядро СУБД.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1c7f0-389">Если вы решили использовать SQLite, скачайте и установите стороннее средство для управления базой данных SQLite и ее просмотра, например [обозреватель базы данных для SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1c7f0-390">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="1c7f0-390">Troubleshooting</span></span>

<span data-ttu-id="1c7f0-391">Если вы столкнулись с проблемой, которую не можете решить, сравните свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1c7f0-392">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте StackOverflow.com, используя [тег ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [тег EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1c7f0-393">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-393">The sample app</span></span>

<span data-ttu-id="1c7f0-394">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1c7f0-395">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1c7f0-396">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-396">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index30.png)

![Страница редактирования учащихся](intro/_static/student-edit30.png)

<span data-ttu-id="1c7f0-399">Стиль пользовательского интерфейса для этого сайта основан на встроенных шаблонах проектов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1c7f0-400">Это руководство посвящено использованию EF Core, а не настройке пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="1c7f0-401">Чтобы получить исходный код готового проекта, перейдите по ссылке в верхней части страницы.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="1c7f0-402">В папке *cu30* содержится код для версии учебника по ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="1c7f0-403">Файлы, отражающие состояние кода для учебников 1–7, находятся в папке *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-405">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1c7f0-406">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-406">Build the project.</span></span>
* <span data-ttu-id="1c7f0-407">В консоли диспетчера пакетов (PMC) выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="1c7f0-408">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-410">Чтобы запустить приложение после скачивания готового проекта, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1c7f0-411">Удалите файл *ContosoUniversity.csproj*, а файл *ContosoUniversitySQLite.csproj* переименуйте в *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="1c7f0-412">В файле *Program.cs* закомментируйте `#define Startup`, чтобы использовать `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="1c7f0-413">Удалите файл *appSettings.json*, а файл *appSettingsSQLite.json* переименуйте в *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="1c7f0-414">Удалите папку *Migrations*, а папку *MigrationsSQL* переименуйте в *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="1c7f0-415">Выполните глобальный поиск `#if SQLiteVersion` и удалите `#if SQLiteVersion` и связанную инструкцию `#endif`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="1c7f0-416">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-416">Build the project.</span></span>
* <span data-ttu-id="1c7f0-417">В командной строке в папке проекта выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="1c7f0-418">В средстве SQLite выполните следующую инструкцию SQL:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="1c7f0-419">Запустите проект, чтобы заполнить базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="1c7f0-420">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-422">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1c7f0-423">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1c7f0-424">Назовите проект *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="1c7f0-425">Очень важно использовать именно такое имя с учетом регистра символов, чтобы пространства имен совпадали при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="1c7f0-426">Выберите в раскрывающихся списках пункты **.NET Core** и **ASP.NET Core 3.0**, а затем выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-428">В терминале перейдите в папку, в которой следует создать папку проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="1c7f0-429">Выполните следующие команды, чтобы создать проект Razor Pages и перейти (`cd`) в новую папку проекта:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1c7f0-430">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="1c7f0-430">Set up the site style</span></span>

<span data-ttu-id="1c7f0-431">Настройте заголовок сайта, нижний колонтитул и меню, внеся изменения в файл *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="1c7f0-432">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="1c7f0-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1c7f0-433">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-433">There are three occurrences.</span></span>

* <span data-ttu-id="1c7f0-434">Удалите пункты меню **Home** (Главная) и **Privacy** (Конфиденциальность). Добавьте пункты **About** (Сведения), **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="1c7f0-435">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="1c7f0-436">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст об ASP.NET Core описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="1c7f0-437">Запустите приложение, чтобы проверить правильность отображения домашней страницы.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1c7f0-438">Модель данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-438">The data model</span></span>

<span data-ttu-id="1c7f0-439">В следующих разделах создается модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-439">The following sections create a data model:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="1c7f0-441">Учащийся может зарегистрироваться в любом количестве курсов, а в отдельном курсе может быть зарегистрировано любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1c7f0-442">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-442">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

* <span data-ttu-id="1c7f0-444">Создайте папку *Models* (Модели) в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="1c7f0-445">Создайте файл *Models/Student.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1c7f0-446">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1c7f0-447">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1c7f0-448">Поэтому альтернативным автоматически распознаваемым именем для первичного ключа класса `Student` является `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1c7f0-449">Дополнительные сведения см. в разделе [EF Core — Управление ключами](/ef/core/modeling/keys?tabs=data-annotations)</span><span class="sxs-lookup"><span data-stu-id="1c7f0-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1c7f0-450">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1c7f0-451">Свойства навигации содержат другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1c7f0-452">В этом случае свойство `Enrollments` сущности `Student` содержит все сущности `Enrollment`, которые связаны с этим учащимся.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1c7f0-453">Например, если строка Student (Учащийся) в базе данных имеет две связанные строки Enrollment (Регистрация), свойство навигации `Enrollments` содержит две эти сущности Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1c7f0-454">В базе данных строка Enrollment связана со строкой Student, если ее столбец StudentID содержит идентификатор учащегося.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1c7f0-455">Например, предположим, что строка Student содержит идентификатор 1.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1c7f0-456">Связанные строки Enrollment будут содержать значение StudentID (идентификатор учащегося), равное 1.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1c7f0-457">StudentID — это *внешний ключ* в таблице Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1c7f0-458">Свойство `Enrollments` определено как `ICollection<Enrollment>`, так как может быть несколько связанных сущностей Enrollment.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1c7f0-459">Можно использовать и другие типы коллекций, например `List<Enrollment>` или `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1c7f0-460">Если используется `ICollection<Enrollment>`, платформа EF Core по умолчанию создает коллекцию `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1c7f0-461">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="1c7f0-461">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="1c7f0-463">Создайте файл *Models/Enrollment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1c7f0-464">Свойство `EnrollmentID` является первичным ключом. В этой сущности используется шаблон `classnameID` вместо `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1c7f0-465">Для рабочей модели данных выберите один шаблон и используйте только его.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1c7f0-466">В этом учебнике используются оба шаблона, чтобы проиллюстрировать их работу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1c7f0-467">Использование `ID` без `classname` упрощает внесение некоторых изменений в модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1c7f0-468">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1c7f0-469">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade`[допускает значение NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1c7f0-470">Оценка со значением NULL отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1c7f0-471">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1c7f0-472">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1c7f0-473">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1c7f0-474">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1c7f0-475">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1c7f0-476">Например, `StudentID` является внешним ключом для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1c7f0-477">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1c7f0-478">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1c7f0-479">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="1c7f0-479">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="1c7f0-481">Создайте файл *Models/Course.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1c7f0-482">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1c7f0-483">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1c7f0-484">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1c7f0-485">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1c7f0-486">Формирование шаблона для страниц Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-486">Scaffold Student pages</span></span>

<span data-ttu-id="1c7f0-487">В этом разделе вы используете средство формирования шаблонов ASP.NET Core для создания указанных ниже компонентов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1c7f0-488">Класс *контекста* EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-488">An EF Core *context* class.</span></span> <span data-ttu-id="1c7f0-489">Контекст —это основной класс, который координирует функциональные возможности Entity Framework для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1c7f0-490">Он является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="1c7f0-491">Razor Pages с поддержкой операций создания, чтения, обновления и удаления (CRUD) для сущности `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-493">В папке *Pages* создайте папку *Students*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="1c7f0-494">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1c7f0-495">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1c7f0-496">В диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1c7f0-497">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1c7f0-498">В строке **Класс контекста данных** щелкните знак плюса ( **+** ).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="1c7f0-499">Измените имя контекста данных с *ContosoUniversity.Models.ContosoUniversityContext* на *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="1c7f0-500">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-500">Select **Add**.</span></span>

<span data-ttu-id="1c7f0-501">Следующие пакеты устанавливаются автоматически:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-503">Выполните следующие команды интерфейса командной строки .NET Core, чтобы установить необходимые пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="1c7f0-504">Пакет Microsoft.VisualStudio.Web.CodeGeneration.Design требуется для формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1c7f0-505">Хотя приложение не будет использовать SQL Server, средству формирования шаблонов требуется пакет SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1c7f0-506">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1c7f0-507">Выполните приведенную ниже команду, чтобы установить [средство формирования шаблонов aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="1c7f0-508">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц Student.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1c7f0-509">**В Windows**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="1c7f0-510">**В macOS или Linux**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="1c7f0-511">Если в предыдущем шаге возникает проблема, выполните сборку проекта и повторите шаг формирования шаблона.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1c7f0-512">В процессе формирования шаблона выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-512">The scaffolding process:</span></span>

* <span data-ttu-id="1c7f0-513">создаются страницы Razor в папке *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1c7f0-514">*Create.cshtml* и *Create.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-515">*Delete.cshtml* и *Delete.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-516">*Details.cshtml* и *Details.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-517">*Edit.cshtml* и *Edit.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1c7f0-518">*Index.cshtml* и *Index.cshtml.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1c7f0-519">создается файл *Data/SchoolContext.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1c7f0-520">добавляется контекст для внедрения зависимостей в файле *Startup.cs*;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1c7f0-521">добавляет строку подключения к базе данных в файл *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1c7f0-522">Строка подключения к базе данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-524">В файле *appsettings.json* указывается строка подключения для [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="1c7f0-525">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1c7f0-526">По умолчанию LocalDB создает файлы *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-528">Измените строку подключения так, чтобы она указывала на файл базы данных SQLite с именем *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1c7f0-529">Обновление класса контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-529">Update the database context class</span></span>

<span data-ttu-id="1c7f0-530">Контекст базы данных — это основной класс, который координирует функциональные возможности EF Core для определенной модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1c7f0-531">Контекст наследуется от [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1c7f0-532">Контекст указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1c7f0-533">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1c7f0-534">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1c7f0-535">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1c7f0-536">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-536">In EF Core terminology:</span></span>

* <span data-ttu-id="1c7f0-537">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="1c7f0-538">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1c7f0-539">Так как набор сущностей содержит несколько сущностей, свойства DBSet должны иметь имена во множественном числе.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="1c7f0-540">Так как средство формирования шаблонов создало DBSet `Student`, в этом шаге его имя меняется на имя во множественном числе: `Students`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="1c7f0-541">Чтобы код Razor Pages соответствовал новому имени DBSet, измените `_context.Student` на `_context.Students` во всем проекте.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="1c7f0-542">Всего это имя встречается 8 раз.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-542">There are 8 occurrences.</span></span>

<span data-ttu-id="1c7f0-543">Выполните сборку проекта и убедитесь в отсутствии ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1c7f0-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1c7f0-544">Startup.cs</span></span>

<span data-ttu-id="1c7f0-545">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1c7f0-546">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1c7f0-547">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1c7f0-548">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1c7f0-549">Средство формирования шаблонов автоматически зарегистрировало класс контекста в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-551">Выделенные строки в `ConfigureServices` были добавлены средством формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-553">Убедитесь в том, что код, добавленный средством формирования шаблонов в `ConfigureServices`, вызывает `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="1c7f0-554">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1c7f0-555">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1c7f0-556">Создание базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-556">Create the database</span></span>

<span data-ttu-id="1c7f0-557">Обновите файл *Program.cs*, чтобы создать базу данных, если она не существует.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1c7f0-558">Метод [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) не выполняет никаких действий, если база данных для контекста существует.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1c7f0-559">Если база данных не существует, она создается вместе со схемой.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1c7f0-560">`EnsureCreated` обеспечивает описанный ниже рабочий процесс для обработки изменений модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1c7f0-561">База данных удаляется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-561">Delete the database.</span></span> <span data-ttu-id="1c7f0-562">Все существующие данные теряются.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-562">Any existing data is lost.</span></span>
* <span data-ttu-id="1c7f0-563">Модель данных изменяется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-563">Change the data model.</span></span> <span data-ttu-id="1c7f0-564">Например, добавляется поле `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1c7f0-565">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-565">Run the app.</span></span>
* <span data-ttu-id="1c7f0-566">Метод `EnsureCreated` создает базу данных с новой схемой.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1c7f0-567">Этот рабочий процесс хорошо подходит для ранних стадий разработки, когда схема часто меняется, если данные сохранять не требуется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1c7f0-568">Однако если данные, введенные в базу данных, необходимо сохранять, ситуация будет иной.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1c7f0-569">В таком случае используйте перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="1c7f0-570">Далее в этой серии учебников вы удалите базу данных, созданную методом `EnsureCreated`, и используете вместо этого перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1c7f0-571">Созданную методом `EnsureCreated` базу данных нельзя обновить, используя перенос.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1c7f0-572">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-572">Test the app</span></span>

* <span data-ttu-id="1c7f0-573">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-573">Run the app.</span></span>
* <span data-ttu-id="1c7f0-574">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1c7f0-575">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1c7f0-576">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-576">Seed the database</span></span>

<span data-ttu-id="1c7f0-577">Метод `EnsureCreated` создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1c7f0-578">В этом разделе добавляется код, который заполняет базу данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1c7f0-579">Создайте файл *Data/DbInitializer.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1c7f0-580">Этот код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1c7f0-581">Если учащихся нет, в базу данных добавляются тестовые данные.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1c7f0-582">Для повышения производительности тестовые данные создаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="1c7f0-583">В файле *Program.cs* замените вызов `EnsureCreated` вызовом `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-585">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="1c7f0-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-587">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1c7f0-588">Перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-588">Restart the app.</span></span>

* <span data-ttu-id="1c7f0-589">Выберите страницу учащихся, чтобы увидеть заполненные данные.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1c7f0-590">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-592">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1c7f0-593">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1c7f0-594">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1c7f0-595">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1c7f0-596">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1c7f0-597">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотреть код**, чтобы увидеть, как модель `Student` соотносится со схемой таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-599">Используйте средство SQLite для просмотра схемы базы данных и заполненных данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1c7f0-600">Файл базы данных называется *CU.db* и находится в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1c7f0-601">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="1c7f0-601">Asynchronous code</span></span>

<span data-ttu-id="1c7f0-602">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1c7f0-603">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1c7f0-604">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1c7f0-605">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1c7f0-606">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1c7f0-607">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1c7f0-608">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1c7f0-609">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1c7f0-610">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1c7f0-611">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1c7f0-612">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1c7f0-613">создавать возвращаемый объект [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1c7f0-614">Тип возвращаемого значения `Task<T>` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1c7f0-615">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1c7f0-616">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1c7f0-617">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1c7f0-618">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1c7f0-619">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1c7f0-620">Асинхронно выполняются только те инструкции, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1c7f0-621">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1c7f0-622">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1c7f0-623">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1c7f0-624">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь в том, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1c7f0-625">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c7f0-626">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="1c7f0-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1c7f0-627">Следующий учебник</span><span class="sxs-lookup"><span data-stu-id="1c7f0-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c7f0-628">На примере веб-приложения Contoso University показано, как создать веб-приложение Razor Pages ASP.NET Core с помощью Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="1c7f0-629">В этом примере приложения реализуется веб-сайт вымышленного университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1c7f0-630">На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1c7f0-631">Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="1c7f0-632">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1c7f0-633">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c7f0-634">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1c7f0-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="1c7f0-637">Опыт работы с [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="1c7f0-638">Начинающие программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1c7f0-639">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="1c7f0-639">Troubleshooting</span></span>

<span data-ttu-id="1c7f0-640">Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового проекта](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1c7f0-641">Чтобы получить помощь, вы можете опубликовать вопрос на веб-сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделе, посвященном [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="1c7f0-642">Веб-приложение университета Contoso</span><span class="sxs-lookup"><span data-stu-id="1c7f0-642">The Contoso University web app</span></span>

<span data-ttu-id="1c7f0-643">Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="1c7f0-644">Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1c7f0-645">Здесь приведено несколько экранов, создаваемых в руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-645">Here are a few of the screens created in the tutorial.</span></span>

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

<span data-ttu-id="1c7f0-648">Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="1c7f0-649">Это руководство посвящено использованию EF Core с Razor Pages, а не работе с пользовательским интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="1c7f0-650">Создание веб-приложения Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="1c7f0-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-652">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1c7f0-653">Создайте новое веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="1c7f0-654">Назовите проект **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="1c7f0-655">Очень важно, чтобы проект имел имя *ContosoUniversity*, чтобы совпадали пространства имен при копировании и вставке кода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="1c7f0-656">Выберите в раскрывающемся списке **ASP.NET Core 2.1**, а затем **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="1c7f0-657">Изображения для приведенных выше шагов см. в разделе [Создание веб-приложения Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="1c7f0-658">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1c7f0-660">Настройка стиля сайта</span><span class="sxs-lookup"><span data-stu-id="1c7f0-660">Set up the site style</span></span>

<span data-ttu-id="1c7f0-661">Выполните небольшую настройку меню, макета и домашней страницы сайта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="1c7f0-662">Внесите следующие изменения в файл *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="1c7f0-663">Замените все вхождения "ContosoUniversity" на "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="1c7f0-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1c7f0-664">Таких элементов будет три.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-664">There are three occurrences.</span></span>

* <span data-ttu-id="1c7f0-665">Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="1c7f0-666">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-666">The changes are highlighted.</span></span> <span data-ttu-id="1c7f0-667">(Вся разметка *не* отображается.)</span><span class="sxs-lookup"><span data-stu-id="1c7f0-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="1c7f0-668">Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="1c7f0-669">Создание модели данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-669">Create the data model</span></span>

<span data-ttu-id="1c7f0-670">Создайте классы сущностей для приложения университета Contoso.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="1c7f0-671">Начните со следующих трех сущностей:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-671">Start with the following three entities:</span></span>

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

<span data-ttu-id="1c7f0-673">Между сущностями `Student` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="1c7f0-674">Между сущностями `Course` и `Enrollment` действует связь один ко многим.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="1c7f0-675">Студент может записаться на любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="1c7f0-676">На курс может быть зачислено любое количество учащихся.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="1c7f0-677">В следующих разделах создается класс для каждой из этих сущностей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="1c7f0-678">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-678">The Student entity</span></span>

![Схема сущности Student](intro/_static/student-entity.png)

<span data-ttu-id="1c7f0-680">Создайте папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-680">Create a *Models* folder.</span></span> <span data-ttu-id="1c7f0-681">В папке *Models* создайте файл класса с именем *Student.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="1c7f0-682">Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="1c7f0-683">По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1c7f0-684">В `classnameID``classname` — это имя класса.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="1c7f0-685">Альтернативным автоматически распознаваемым первичным ключом является `StudentID` в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="1c7f0-686">Свойство `Enrollments` является [свойством навигации](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1c7f0-687">Свойства навигации ссылаются на другие сущности, связанные с этой сущностью.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="1c7f0-688">В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="1c7f0-689">Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="1c7f0-690">Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="1c7f0-691">Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="1c7f0-692">Таблица `Enrollment` содержит две строки с `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="1c7f0-693">`StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="1c7f0-694">Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="1c7f0-695">Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="1c7f0-696">Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="1c7f0-697">Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="1c7f0-698">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="1c7f0-698">The Enrollment entity</span></span>

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="1c7f0-700">В папке *Models* создайте файл *Enrollment.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="1c7f0-701">Свойство `EnrollmentID` является первичным ключом.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="1c7f0-702">Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="1c7f0-703">Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="1c7f0-704">В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="1c7f0-705">Свойство `Grade` имеет тип `enum`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1c7f0-706">Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="1c7f0-707">Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1c7f0-708">Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1c7f0-709">Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="1c7f0-710">Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="1c7f0-711">Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1c7f0-712">Сущность `Enrollment` связана с одной сущностью `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1c7f0-713">EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1c7f0-714">Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1c7f0-715">Свойства внешнего ключа также могут называться `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1c7f0-716">Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="1c7f0-717">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="1c7f0-717">The Course entity</span></span>

![Схема сущности Course](intro/_static/course-entity.png)

<span data-ttu-id="1c7f0-719">В папке *Models* создайте файл *Course.cs*, содержащий следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="1c7f0-720">Свойство `Enrollments` является свойством навигации.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1c7f0-721">Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1c7f0-722">Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="1c7f0-723">Формирование шаблона для модели Student</span><span class="sxs-lookup"><span data-stu-id="1c7f0-723">Scaffold the student model</span></span>

<span data-ttu-id="1c7f0-724">В этом разделе формируется шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="1c7f0-725">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели Student.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="1c7f0-726">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-726">Build the project.</span></span>
* <span data-ttu-id="1c7f0-727">Создайте папку *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c7f0-729">В **обозревателе решений** щелкните правой кнопкой мыши папку *Pages/Students* и выберите **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1c7f0-730">В диалоговом окне **Добавление шаблона** щелкните **Razor Pages на основе Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="1c7f0-731">Заполните поля в диалоговом окне **Добавление Razor Pages на основе Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="1c7f0-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1c7f0-732">В раскрывающемся списке **Класс модели** выберите **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="1c7f0-733">В строке **Класс контекста данных** щелкните значок плюса ( **+** ) и измените автоматически присвоенное имя на **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="1c7f0-734">В раскрывающемся списке **Класс контекста данных** выберите **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="1c7f0-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="1c7f0-735">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-735">Select **Add**.</span></span>

![Диалоговое окно CRUD](intro/_static/s1.png)

<span data-ttu-id="1c7f0-737">Если на предыдущем шаге у вас возникли проблемы, см. раздел [Создание модели фильма](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c7f0-739">Выполните следующую команду, чтобы сформировать шаблон для модели Student.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="1c7f0-740">В процессе формирования шаблонов были созданы и изменены следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1c7f0-741">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="1c7f0-741">Files created</span></span>

* <span data-ttu-id="1c7f0-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="1c7f0-743">*Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="1c7f0-744">Обновления файла</span><span class="sxs-lookup"><span data-stu-id="1c7f0-744">File updates</span></span>

* <span data-ttu-id="1c7f0-745">*Startup.cs*: изменения в этом файле подробно описываются в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="1c7f0-746">*appsettings.json* : добавляется строка подключения, используемая для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1c7f0-747">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="1c7f0-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1c7f0-748">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1c7f0-749">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1c7f0-750">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1c7f0-751">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1c7f0-752">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1c7f0-753">Проверьте метод `ConfigureServices` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="1c7f0-754">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="1c7f0-755">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1c7f0-756">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="1c7f0-757">Обновление метода Main</span><span class="sxs-lookup"><span data-stu-id="1c7f0-757">Update main</span></span>

<span data-ttu-id="1c7f0-758">В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="1c7f0-759">Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="1c7f0-760">Вызовите [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="1c7f0-761">Высвободите контекст после завершения работы метода `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="1c7f0-762">В следующем примере кода показан обновленный файл *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="1c7f0-763">`EnsureCreated` позволяет проверить существование базы данных для контекста.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="1c7f0-764">Если контекст существует, никаких действий не предпринимается.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-764">If it exists, no action is taken.</span></span> <span data-ttu-id="1c7f0-765">Если контекст не существует, создаются база данных и вся ее схема.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="1c7f0-766">`EnsureCreated` не использует миграции для создания базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="1c7f0-767">Созданную с помощью `EnsureCreated` базу данных впоследствии нельзя обновить, используя миграции.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="1c7f0-768">`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="1c7f0-769">Удалите базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-769">Delete the DB.</span></span>
* <span data-ttu-id="1c7f0-770">Измените схему базы данных (например, добавьте поле `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="1c7f0-771">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-771">Run the app.</span></span>
* <span data-ttu-id="1c7f0-772">`EnsureCreated` создает базу данных со столбцом `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="1c7f0-773">`EnsureCreated` удобно использовать на ранних стадиях разработки, когда схема часто меняется.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="1c7f0-774">Далее в этом учебнике база данных удаляется и используются миграции.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1c7f0-775">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="1c7f0-775">Test the app</span></span>

<span data-ttu-id="1c7f0-776">Запустите приложение и примите политику использования файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="1c7f0-777">Это приложение не хранит персональные данные.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="1c7f0-778">Сведения о политике использования файлов cookie можно прочитать в разделе [Поддержка общего регламента по защите данных ЕС (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="1c7f0-779">Щелкните ссылку **Students** и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1c7f0-780">Протестируйте ссылки Edit, Details и Delete.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="1c7f0-781">Проверка контекста базы данных SchoolContext</span><span class="sxs-lookup"><span data-stu-id="1c7f0-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="1c7f0-782">Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="1c7f0-783">Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1c7f0-784">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1c7f0-785">В этом проекте соответствующий класс называется `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1c7f0-786">Обновите файл *SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="1c7f0-787">Выделенный код создает свойство [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для каждого набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1c7f0-788">В терминологии EF Core:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-788">In EF Core terminology:</span></span>

* <span data-ttu-id="1c7f0-789">Набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="1c7f0-790">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1c7f0-791">`DbSet<Enrollment>` и `DbSet<Course>` можно опустить.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="1c7f0-792">Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="1c7f0-793">В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="1c7f0-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1c7f0-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1c7f0-795">Строка подключения указывает базу данных [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="1c7f0-796">LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1c7f0-797">LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1c7f0-798">По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="1c7f0-799">Добавление кода для инициализации базы данных с использованием тестовых данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="1c7f0-800">EF Core создает пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="1c7f0-801">В этом разделе создается метод `Initialize`, предназначенный для заполнения тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="1c7f0-802">В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="1c7f0-803">Примечание. Предыдущий код использует `Models` для пространства имен (`namespace ContosoUniversity.Models`) вместо `Data`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="1c7f0-804">`Models` согласуется с кодом, созданным средством формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="1c7f0-805">См. дополнительные сведения о [проблеме с формированием шаблонов на GitHub ](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="1c7f0-806">Код проверяет наличие учащихся в базе данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="1c7f0-807">Если учащихся в базе данных нет, она заполняется тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="1c7f0-808">Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1c7f0-809">Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="1c7f0-810">Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="1c7f0-811">В файле *Program.cs* измените метод `Main`, чтобы реализовать вызов `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="1c7f0-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c7f0-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c7f0-813">Завершите работу приложения, если оно запущено, и выполните следующую команду в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="1c7f0-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c7f0-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c7f0-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c7f0-815">Остановите приложение, если оно выполняется, и удалите файл *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="1c7f0-816">Просмотр базы данных</span><span class="sxs-lookup"><span data-stu-id="1c7f0-816">View the DB</span></span>

<span data-ttu-id="1c7f0-817">Имя базы данных создается на основе имени контекста, которое вы указали ранее, а также включает дефис и GUID.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="1c7f0-818">Таким образом, имя базы данных будет SchoolContext-{GUID}.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="1c7f0-819">GUID будет отличаться для каждого пользователя.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="1c7f0-820">Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="1c7f0-821">В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="1c7f0-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="1c7f0-822">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="1c7f0-823">Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="1c7f0-824">Асинхронный код</span><span class="sxs-lookup"><span data-stu-id="1c7f0-824">Asynchronous code</span></span>

<span data-ttu-id="1c7f0-825">Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1c7f0-826">Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1c7f0-827">В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1c7f0-828">В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1c7f0-829">В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1c7f0-830">Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="1c7f0-831">Во время выполнения асинхронный код использует немного больше служебных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1c7f0-832">Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1c7f0-833">В следующем коде для асинхронного выполнения используются ключевое слово [async](/dotnet/csharp/language-reference/keywords/async), возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="1c7f0-834">Ключевое слово `async` указывает компилятору:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1c7f0-835">создавать обратные вызовы для частей тела метода;</span><span class="sxs-lookup"><span data-stu-id="1c7f0-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1c7f0-836">автоматически создавать возвращаемый объект [Task](/dotnet/api/system.threading.tasks.task).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="1c7f0-837">Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="1c7f0-838">Неявный тип возвращаемого значения `Task` представляет текущую операцию.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="1c7f0-839">Ключевое слово `await` предписывает компилятору разделить метод на две части.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1c7f0-840">Первая часть завершается операцией, которая запускается в асинхронном режиме.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1c7f0-841">Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1c7f0-842">`ToListAsync` является асинхронной версией метода расширения `ToList`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1c7f0-843">При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:</span><span class="sxs-lookup"><span data-stu-id="1c7f0-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1c7f0-844">Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="1c7f0-845">К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1c7f0-846">В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1c7f0-847">Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1c7f0-848">Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.</span><span class="sxs-lookup"><span data-stu-id="1c7f0-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="1c7f0-849">Дополнительные сведения об асинхронном программировании см. в разделах [Обзор асинхронной модели](/dotnet/standard/async) и [Асинхронное программирование с использованием ключевых слов Async и Await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="1c7f0-850">Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).</span><span class="sxs-lookup"><span data-stu-id="1c7f0-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="1c7f0-851">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1c7f0-851">Additional resources</span></span>

* [<span data-ttu-id="1c7f0-852">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="1c7f0-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="1c7f0-853">Вперед</span><span class="sxs-lookup"><span data-stu-id="1c7f0-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
