---
title: Учебник. Использование ASP.NET MVC с EF Core. Реализация наследования
description: В этом учебнике показано, как реализовать наследование в модели данных с использованием платформы Entity Framework Core в приложении ASP.NET Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: data/ef-mvc/inheritance
ms.openlocfilehash: cea19044294482adf834d998b75e0f3fc386cc46
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400535"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="ef09e-103">Учебник. Использование ASP.NET MVC с EF Core. Реализация наследования</span><span class="sxs-lookup"><span data-stu-id="ef09e-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="ef09e-104">В предыдущем учебнике была показана обработка исключений параллелизма.</span><span class="sxs-lookup"><span data-stu-id="ef09e-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="ef09e-105">В этом учебнике демонстрируется, как реализовать наследование в модели данных.</span><span class="sxs-lookup"><span data-stu-id="ef09e-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="ef09e-106">В объектно-ориентированном программировании наследование применяется для оптимизации повторного использования кода.</span><span class="sxs-lookup"><span data-stu-id="ef09e-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="ef09e-107">В рамках этого учебника вы измените классы `Instructor` и `Student` таким образом, чтобы они были производными от базового класса `Person`, который содержит общие свойства для преподавателей и учащихся, такие как `LastName`.</span><span class="sxs-lookup"><span data-stu-id="ef09e-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="ef09e-108">Изменения вносятся в коде, а не на веб-страницах, и автоматически отражаются в базе данных.</span><span class="sxs-lookup"><span data-stu-id="ef09e-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="ef09e-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="ef09e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef09e-110">Сопоставление наследования с базой данных</span><span class="sxs-lookup"><span data-stu-id="ef09e-110">Map inheritance to database</span></span>
> * <span data-ttu-id="ef09e-111">Создание класса Person</span><span class="sxs-lookup"><span data-stu-id="ef09e-111">Create the Person class</span></span>
> * <span data-ttu-id="ef09e-112">Обновление Instructor и Student</span><span class="sxs-lookup"><span data-stu-id="ef09e-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="ef09e-113">Добавление Person в модель</span><span class="sxs-lookup"><span data-stu-id="ef09e-113">Add Person to the model</span></span>
> * <span data-ttu-id="ef09e-114">Создание и обновление миграций</span><span class="sxs-lookup"><span data-stu-id="ef09e-114">Create and update migrations</span></span>
> * <span data-ttu-id="ef09e-115">Тестирование реализации</span><span class="sxs-lookup"><span data-stu-id="ef09e-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ef09e-116">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ef09e-116">Prerequisites</span></span>

* [<span data-ttu-id="ef09e-117">Обработка параллелизма</span><span class="sxs-lookup"><span data-stu-id="ef09e-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="ef09e-118">Сопоставление наследования с базой данных</span><span class="sxs-lookup"><span data-stu-id="ef09e-118">Map inheritance to database</span></span>

<span data-ttu-id="ef09e-119">Классы `Instructor` и `Student` в модели данных School имеют несколько идентичных свойств:</span><span class="sxs-lookup"><span data-stu-id="ef09e-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Классы Student и Instructor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="ef09e-121">Предположим, что вам требуется исключить повторяющийся код для свойств, которые являются общими для сущностей `Instructor` и `Student`.</span><span class="sxs-lookup"><span data-stu-id="ef09e-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="ef09e-122">Кроме того, вам может потребоваться написать службу, которая может форматировать имена как преподавателей, так и учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="ef09e-123">В таких сценариях можно создать базовый класс `Person`, который содержит только общие свойства, а затем унаследовать от него классы `Instructor` и `Student`, как показано на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="ef09e-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Классы Student и Instructor, производные от класса Person](inheritance/_static/inheritance.png)

<span data-ttu-id="ef09e-125">Структура наследования может быть представлена в базе данных несколькими способами.</span><span class="sxs-lookup"><span data-stu-id="ef09e-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="ef09e-126">Вы можете создать таблицу Person, которая будет содержать одновременно информацию о преподавателях и учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="ef09e-127">Некоторые столбцы могут относиться только к преподавателям (HireDate), некоторые только к учащимся (EnrollmentDate), а некоторые одновременно к обеим сущностям (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="ef09e-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="ef09e-128">Как правило, используется столбец дискриминатора, который указывает на тип, представленный соответствующей строкой.</span><span class="sxs-lookup"><span data-stu-id="ef09e-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="ef09e-129">Например, в столбце дискриминатора может указываться значение "Instructor" для преподавателей и "Student" для учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Пример наследования типа "одна таблица на иерархию"](inheritance/_static/tph.png)

<span data-ttu-id="ef09e-131">Такая модель, описывающая формирование структуры наследования сущностей на основе одной таблицы базы данных, называется наследованием типа "одна таблица на иерархию".</span><span class="sxs-lookup"><span data-stu-id="ef09e-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="ef09e-132">В качестве альтернативы можно создать базу данных, которая будет иметь приближенный к структуре наследования вид.</span><span class="sxs-lookup"><span data-stu-id="ef09e-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="ef09e-133">Например, можно хранить в таблице Person только поля с именами и создать отдельные таблицы Instructor и Student с полями дат.</span><span class="sxs-lookup"><span data-stu-id="ef09e-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Наследование типа «одна таблица на тип»](inheritance/_static/tpt.png)

<span data-ttu-id="ef09e-135">Такая модель создания таблицы базы данных для каждого класса сущности называется наследованием типа "одна таблица на тип".</span><span class="sxs-lookup"><span data-stu-id="ef09e-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="ef09e-136">Кроме того, можно сопоставить все не являющиеся абстрактными типы с отдельными таблицами.</span><span class="sxs-lookup"><span data-stu-id="ef09e-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="ef09e-137">Все свойства класса, включая унаследованные, сопоставляются со столбцами в соответствующей таблице.</span><span class="sxs-lookup"><span data-stu-id="ef09e-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="ef09e-138">Такая модель называется наследованием типа "одна таблица на конкретный класс".</span><span class="sxs-lookup"><span data-stu-id="ef09e-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="ef09e-139">Если реализовать наследование типа "одна таблица на конкретный класс" для показанных выше классов Person, Student и Instructor, таблицы Student и Instructor после реализации наследования будут выглядеть так же, как и до этого.</span><span class="sxs-lookup"><span data-stu-id="ef09e-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="ef09e-140">Модели наследования "одна таблица на иерархию" и "одна таблица на конкретный класс", как правило, обеспечивают более высокую производительность по сравнению с моделью "одна таблица на тип", поскольку при использовании последней могут выполняться сложные запросы на соединение.</span><span class="sxs-lookup"><span data-stu-id="ef09e-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="ef09e-141">В этом учебнике демонстрируется реализация модели наследования "одна таблица на иерархию".</span><span class="sxs-lookup"><span data-stu-id="ef09e-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="ef09e-142">Платформа Entity Framework поддерживает только модель наследования "одна таблица на иерархию".</span><span class="sxs-lookup"><span data-stu-id="ef09e-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="ef09e-143">Вам необходимо создать класс `Person`, изменить классы `Instructor` и `Student` так, чтобы они были производными от класса `Person`, добавить новый класс в `DbContext`, после чего создать миграцию.</span><span class="sxs-lookup"><span data-stu-id="ef09e-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="ef09e-144">Перед внесением изменений рекомендуется сохранить копию проекта.</span><span class="sxs-lookup"><span data-stu-id="ef09e-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="ef09e-145">В этом случае, если возникнут проблемы, вы сможете вернуться к сохраненному проекту вместо того, чтобы отменять выполненные в рамках этого учебника действия или начинать всю серию сначала.</span><span class="sxs-lookup"><span data-stu-id="ef09e-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="ef09e-146">Создание класса Person</span><span class="sxs-lookup"><span data-stu-id="ef09e-146">Create the Person class</span></span>

<span data-ttu-id="ef09e-147">В папке Models создайте файл Person.cs и замените код шаблона следующим:</span><span class="sxs-lookup"><span data-stu-id="ef09e-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="ef09e-148">Обновление Instructor и Student</span><span class="sxs-lookup"><span data-stu-id="ef09e-148">Update Instructor and Student</span></span>

<span data-ttu-id="ef09e-149">В файле *Instructor.cs* измените класс Instructor так, чтобы он был производным от класса Person, и удалите поля ключа и имени.</span><span class="sxs-lookup"><span data-stu-id="ef09e-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="ef09e-150">Код будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ef09e-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="ef09e-151">Выполните те же изменения в файле *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="ef09e-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="ef09e-152">Добавление Person в модель</span><span class="sxs-lookup"><span data-stu-id="ef09e-152">Add Person to the model</span></span>

<span data-ttu-id="ef09e-153">Добавьте тип сущности Person в файл *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="ef09e-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="ef09e-154">Новые строки выделены.</span><span class="sxs-lookup"><span data-stu-id="ef09e-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="ef09e-155">Это все, что требуется платформе Entity Framework для настройки наследования типа "одна таблица на иерархию".</span><span class="sxs-lookup"><span data-stu-id="ef09e-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="ef09e-156">Как видно, после обновления базы данных в ней будет присутствовать таблица Person вместо таблиц Student и Instructor.</span><span class="sxs-lookup"><span data-stu-id="ef09e-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="ef09e-157">Создание и обновление миграций</span><span class="sxs-lookup"><span data-stu-id="ef09e-157">Create and update migrations</span></span>

<span data-ttu-id="ef09e-158">Сохраните изменения и выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="ef09e-158">Save your changes and build the project.</span></span> <span data-ttu-id="ef09e-159">Затем откройте командное окно в папке проекта и введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ef09e-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="ef09e-160">На этом этапе не выполняйте команду `database update`.</span><span class="sxs-lookup"><span data-stu-id="ef09e-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="ef09e-161">Ее выполнение приведет к потере данных, поскольку будет удалена таблица Instructor, а таблица Student будет переименована в Person.</span><span class="sxs-lookup"><span data-stu-id="ef09e-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="ef09e-162">Для сохранения существующих данных потребуется настраиваемый код.</span><span class="sxs-lookup"><span data-stu-id="ef09e-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="ef09e-163">Откройте файл *Migrations/\<timestamp>_Inheritance.cs* и замените метод `Up` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="ef09e-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="ef09e-164">Этот код выполняет следующие задачи по обновлению базы данных:</span><span class="sxs-lookup"><span data-stu-id="ef09e-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="ef09e-165">Удаляет ограничения внешнего ключа и индексы, которые указывают на таблицу Student.</span><span class="sxs-lookup"><span data-stu-id="ef09e-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="ef09e-166">Переименовывает таблицу Instructor в Person и вносит изменения, необходимые для сохранения в ней данных из таблицы Student:</span><span class="sxs-lookup"><span data-stu-id="ef09e-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="ef09e-167">Добавляет допускающий значения NULL тип EnrollmentDate для учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="ef09e-168">Добавляет столбец дискриминатора, который указывает, представляет ли строка учащегося или преподавателя.</span><span class="sxs-lookup"><span data-stu-id="ef09e-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="ef09e-169">Изменяет тип HireDate на допускающий значения NULL, поскольку в строках для учащихся не будет указываться дата приема на работу.</span><span class="sxs-lookup"><span data-stu-id="ef09e-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="ef09e-170">Добавляет временное поле, которое будет использоваться для обновления внешних ключей, указывающих на учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="ef09e-171">При копировании записей учащихся в таблицу Person им назначаются новые значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="ef09e-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="ef09e-172">Копирует данные из таблицы Student в таблицу Person.</span><span class="sxs-lookup"><span data-stu-id="ef09e-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="ef09e-173">При этом записям учащихся назначаются новые значения первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="ef09e-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="ef09e-174">Исправляет значения внешнего ключа, которые указывают на учащихся.</span><span class="sxs-lookup"><span data-stu-id="ef09e-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="ef09e-175">Повторно создает ограничения внешнего ключа и индексы, которые после этого указывают на таблицу Person.</span><span class="sxs-lookup"><span data-stu-id="ef09e-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="ef09e-176">(Если вместо целочисленного типа первичного ключа используется GUID, значения первичного ключа для записей учащихся изменять не потребуется и некоторые из этих действий можно пропустить.)</span><span class="sxs-lookup"><span data-stu-id="ef09e-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="ef09e-177">Выполните команду `database update`:</span><span class="sxs-lookup"><span data-stu-id="ef09e-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="ef09e-178">(В рабочей системе необходимо внести соответствующие изменения в метод `Down` на случай, если вам потребуется вернуться к предыдущей версии базы данных.</span><span class="sxs-lookup"><span data-stu-id="ef09e-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="ef09e-179">В этом учебнике метод `Down` не используется.)</span><span class="sxs-lookup"><span data-stu-id="ef09e-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="ef09e-180">При изменении схемы в базе, содержащей существующие данные, возможны другие ошибки.</span><span class="sxs-lookup"><span data-stu-id="ef09e-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="ef09e-181">Если вы получаете ошибки миграции, которые не удается устранить, измените имя базы данных в строке подключения или удалите базу данных.</span><span class="sxs-lookup"><span data-stu-id="ef09e-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="ef09e-182">В новой базе не будет данных, которые требуется перенести, в результате чего команда обновления базы данных с большей долей вероятности завершится без ошибок.</span><span class="sxs-lookup"><span data-stu-id="ef09e-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="ef09e-183">Чтобы удалить базу данных, используйте средство SSOX или выполните команду `database drop` в интерфейсе командной строки.</span><span class="sxs-lookup"><span data-stu-id="ef09e-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="ef09e-184">Тестирование реализации</span><span class="sxs-lookup"><span data-stu-id="ef09e-184">Test the implementation</span></span>

<span data-ttu-id="ef09e-185">Запустите приложение и попробуйте открыть различные страницы.</span><span class="sxs-lookup"><span data-stu-id="ef09e-185">Run the app and try various pages.</span></span> <span data-ttu-id="ef09e-186">Все работает так же, как и раньше.</span><span class="sxs-lookup"><span data-stu-id="ef09e-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="ef09e-187">В **обозревателе объектов SQL Server** разверните узел **Data Connections/SchoolContext** и затем **Таблицы**. Вы увидите, что вместо таблиц Student и Instructor появилась таблица Person.</span><span class="sxs-lookup"><span data-stu-id="ef09e-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="ef09e-188">Откройте таблицу Person в конструкторе и убедитесь, что в ней представлены все столбцы, содержавшиеся в таблицах Student и Instructor.</span><span class="sxs-lookup"><span data-stu-id="ef09e-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Таблица Person в окне SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="ef09e-190">Щелкните таблицу Person правой кнопкой мыши и выберите команду **Показать данные таблицы**, чтобы просмотреть столбец дискриминатора.</span><span class="sxs-lookup"><span data-stu-id="ef09e-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Таблица Person в окне SSOX — данные таблицы](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="ef09e-192">Получите код</span><span class="sxs-lookup"><span data-stu-id="ef09e-192">Get the code</span></span>

[<span data-ttu-id="ef09e-193">Скачайте или ознакомьтесь с готовым приложением.</span><span class="sxs-lookup"><span data-stu-id="ef09e-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="ef09e-194">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ef09e-194">Additional resources</span></span>

<span data-ttu-id="ef09e-195">Дополнительные сведения о наследовании на платформе Entity Framework Core см. в разделе [Наследование](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="ef09e-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ef09e-196">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ef09e-196">Next steps</span></span>

<span data-ttu-id="ef09e-197">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="ef09e-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef09e-198">Сопоставление наследования с базой данных</span><span class="sxs-lookup"><span data-stu-id="ef09e-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="ef09e-199">Создание класса Person</span><span class="sxs-lookup"><span data-stu-id="ef09e-199">Created the Person class</span></span>
> * <span data-ttu-id="ef09e-200">Обновление Instructor и Student</span><span class="sxs-lookup"><span data-stu-id="ef09e-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="ef09e-201">Добавление Person в модель</span><span class="sxs-lookup"><span data-stu-id="ef09e-201">Added Person to the model</span></span>
> * <span data-ttu-id="ef09e-202">Создание и обновление миграций</span><span class="sxs-lookup"><span data-stu-id="ef09e-202">Created and update migrations</span></span>
> * <span data-ttu-id="ef09e-203">Тестирование реализации</span><span class="sxs-lookup"><span data-stu-id="ef09e-203">Tested the implementation</span></span>

<span data-ttu-id="ef09e-204">В следующем учебнике описано, как использовать несколько сценариев Entity Framework с расширенными возможностями.</span><span class="sxs-lookup"><span data-stu-id="ef09e-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ef09e-205">Далее: Дополнительные разделы</span><span class="sxs-lookup"><span data-stu-id="ef09e-205">Next: Advanced topics</span></span>](advanced.md)
