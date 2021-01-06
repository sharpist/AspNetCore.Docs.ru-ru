---
title: Часть 8. Razor Pages с EF Core в ASP.NET Core — параллелизм
author: rick-anderson
description: Часть 8 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053999"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="930a0-103">Часть 8. Razor Pages с EF Core в ASP.NET Core — параллелизм</span><span class="sxs-lookup"><span data-stu-id="930a0-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="930a0-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith)</span><span class="sxs-lookup"><span data-stu-id="930a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="930a0-105">Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей параллельно (одновременно) изменяют одну сущность.</span><span class="sxs-lookup"><span data-stu-id="930a0-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="930a0-106">Конфликты параллелизма</span><span class="sxs-lookup"><span data-stu-id="930a0-106">Concurrency conflicts</span></span>

<span data-ttu-id="930a0-107">Конфликт параллелизма возникает в следующих условиях:</span><span class="sxs-lookup"><span data-stu-id="930a0-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="930a0-108">Пользователь переходит на страницу редактирования для сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="930a0-109">Другой пользователь обновляет ту же сущность до того, как изменение первого пользователя записывается в базу данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="930a0-110">Если обнаружение параллелизма не включено, то пользователь, обновляющий базу данных последним, перезаписывает изменения другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="930a0-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="930a0-111">Если такой риск допустим, стоимость реализации параллелизма может перевесить его преимущества.</span><span class="sxs-lookup"><span data-stu-id="930a0-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="930a0-112">Пессимистичный параллелизм (блокировка)</span><span class="sxs-lookup"><span data-stu-id="930a0-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="930a0-113">Один из способов предотвращения конфликтов параллелизма — блокировка базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="930a0-114">Это называется пессимистичным параллелизмом.</span><span class="sxs-lookup"><span data-stu-id="930a0-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="930a0-115">Прежде чем приложение считывает строку базы данных, которую планируется обновить, оно запрашивает блокировку.</span><span class="sxs-lookup"><span data-stu-id="930a0-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="930a0-116">Когда строка блокируется для обновления, другие пользователи не могут заблокировать ее, пока первая блокировка не будет снята.</span><span class="sxs-lookup"><span data-stu-id="930a0-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="930a0-117">Управление блокировками имеет недостатки.</span><span class="sxs-lookup"><span data-stu-id="930a0-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="930a0-118">Его может быть сложно реализовать, и оно может вызывать проблемы с производительностью по мере увеличения числа пользователей.</span><span class="sxs-lookup"><span data-stu-id="930a0-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="930a0-119">В Entity Framework Core нет встроенной поддержки этой функции, и данное руководство не рассказывает, как ее реализовать.</span><span class="sxs-lookup"><span data-stu-id="930a0-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="930a0-120">Оптимистическая блокировка</span><span class="sxs-lookup"><span data-stu-id="930a0-120">Optimistic concurrency</span></span>

<span data-ttu-id="930a0-121">Оптимистическая блокировка допускает появление конфликтов параллелизма, а затем обрабатывает их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="930a0-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="930a0-122">Например, Мария посещает страницу изменения кафедры и изменяет бюджет кафедры английской языка с 350 000,00 USD на 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Изменение бюджета на 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="930a0-124">Прежде чем Мария нажимает кнопку **Save** (Сохранить), Дмитрий заходит на ту же страницу и изменяет значение в поле "Start Date" (Дата начала) с 9/1/2007 на 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="930a0-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Изменение начальной даты на 2013 г.](concurrency/_static/change-date30.png)

<span data-ttu-id="930a0-126">Сначала Мария нажимает кнопку **Save** (Сохранить) и видит, что ее изменение вступило в силу, так как в браузере отображается страница индекса с нулевым размером бюджета.</span><span class="sxs-lookup"><span data-stu-id="930a0-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="930a0-127">Дмитрий нажимает кнопку **Save** (Сохранить) на странице редактирования, где все еще отображается бюджет 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="930a0-128">Дальнейший ход событий определяется порядком обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="930a0-129">Вы можете отслеживать, для какого свойства пользователь изменил и обновил только соответствующие столбцы в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="930a0-130">В этом сценарии никакие данные не теряются.</span><span class="sxs-lookup"><span data-stu-id="930a0-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="930a0-131">Разные свойства были обновлены двумя пользователями.</span><span class="sxs-lookup"><span data-stu-id="930a0-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="930a0-132">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит изменения, внесенные как Марией, так и Дмитрием.</span><span class="sxs-lookup"><span data-stu-id="930a0-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="930a0-133">Этот способ обновления позволяет снизить количество конфликтов, способных привести к потере данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="930a0-134">Такой подход имеет ряд недостатков.</span><span class="sxs-lookup"><span data-stu-id="930a0-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="930a0-135">Он не позволяет избежать потери данных, если конкурирующие изменения вносятся для одного свойства.</span><span class="sxs-lookup"><span data-stu-id="930a0-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="930a0-136">Он не слишком хорошо подходит для веб-приложений,</span><span class="sxs-lookup"><span data-stu-id="930a0-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="930a0-137">так как требует поддерживать значительный объем состояний, чтобы отслеживать все извлеченные и новые значения.</span><span class="sxs-lookup"><span data-stu-id="930a0-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="930a0-138">Обслуживание большого объема состояний может негативно повлиять на производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="930a0-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="930a0-139">Он может повысить сложность приложений по сравнению с обнаружением параллелизма для сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="930a0-140">Вы можете позволить изменению Дмитрия перезаписать изменение Марии.</span><span class="sxs-lookup"><span data-stu-id="930a0-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="930a0-141">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит дату 9/1/2013 и значение 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="930a0-142">Такой подход называется *победой клиента* или *сохранением последнего внесенного изменения*.</span><span class="sxs-lookup"><span data-stu-id="930a0-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="930a0-143">(Все значения из клиента имеют приоритет над данными в хранилище.) Если не писать код для обработки параллелизма, автоматически применяется победа клиента.</span><span class="sxs-lookup"><span data-stu-id="930a0-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="930a0-144">Вы можете запретить обновление изменения Дмитрия в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="930a0-145">Как правило приложение будет:</span><span class="sxs-lookup"><span data-stu-id="930a0-145">Typically, the app would:</span></span>

  * <span data-ttu-id="930a0-146">отображать сообщение об ошибке;</span><span class="sxs-lookup"><span data-stu-id="930a0-146">Display an error message.</span></span>
  * <span data-ttu-id="930a0-147">отображать текущее состояние данных;</span><span class="sxs-lookup"><span data-stu-id="930a0-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="930a0-148">разрешать пользователю повторно применять изменения.</span><span class="sxs-lookup"><span data-stu-id="930a0-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="930a0-149">Это называется *победой хранилища*.</span><span class="sxs-lookup"><span data-stu-id="930a0-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="930a0-150">(Значения в хранилище имеют приоритет над данными, передаваемыми клиентом.) В этом руководстве вы реализуете сценарий победы хранилища.</span><span class="sxs-lookup"><span data-stu-id="930a0-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="930a0-151">Данный метод гарантирует, что никакие изменения не перезаписываются без оповещения пользователя.</span><span class="sxs-lookup"><span data-stu-id="930a0-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="930a0-152">Обнаружение конфликтов в EF Core</span><span class="sxs-lookup"><span data-stu-id="930a0-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="930a0-153">При обнаружении конфликтов EF Core создает исключения `DbConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="930a0-154">Модель данных необходимо настроить для обнаружения конфликтов.</span><span class="sxs-lookup"><span data-stu-id="930a0-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="930a0-155">Ниже приведены варианты реализации обнаружения конфликтов.</span><span class="sxs-lookup"><span data-stu-id="930a0-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="930a0-156">Настройте в EF Core включение исходных значений столбцов, настроенных как [токены параллелизма](/ef/core/modeling/concurrency), в предложение Where команд Update и Delete.</span><span class="sxs-lookup"><span data-stu-id="930a0-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="930a0-157">При вызове `SaveChanges` предложение Where ищет исходные значения всех свойств, помеченных атрибутом <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute>.</span><span class="sxs-lookup"><span data-stu-id="930a0-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="930a0-158">Инструкция UPDATE не найдет обновляемую строку, если какие-либо свойства токенов параллелизма изменились с момента первого чтения строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="930a0-159">EF Core интерпретирует это как конфликт параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="930a0-160">Для таблиц базы данных со множеством столбцов этот подход может привести к очень большим предложениям Where и потребовать большого объема состояний.</span><span class="sxs-lookup"><span data-stu-id="930a0-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="930a0-161">Поэтому в общем случае данный подход не рекомендуется, кроме того, он не применяется и в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="930a0-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="930a0-162">Включите в таблицу базы данных столбец отслеживания, который позволяет определять, когда была изменена строка.</span><span class="sxs-lookup"><span data-stu-id="930a0-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="930a0-163">В базе данных SQL Server типом данных для столбца отслеживания является `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="930a0-164">Значение `rowversion` является последовательным номером, увеличивающимся при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="930a0-165">В команде Update или Delete предложение Where содержит исходное значение столбца отслеживания (номер исходной версии строки).</span><span class="sxs-lookup"><span data-stu-id="930a0-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="930a0-166">Если обновляемая строка была изменена другим пользователем, значение в столбце `rowversion` отличается от исходного.</span><span class="sxs-lookup"><span data-stu-id="930a0-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="930a0-167">В этом случае инструкции Update или Delete не удается найти строку для обновления из-за предложения Where.</span><span class="sxs-lookup"><span data-stu-id="930a0-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="930a0-168">Если нет строк, затронутых командой Update или Delete, EF Core создает исключение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="930a0-169">Добавление свойства отслеживания</span><span class="sxs-lookup"><span data-stu-id="930a0-169">Add a tracking property</span></span>

<span data-ttu-id="930a0-170">В *Models/Department.cs* добавьте свойство отслеживания RowVersion:</span><span class="sxs-lookup"><span data-stu-id="930a0-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="930a0-171">Именно атрибут <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> определяет столбец как столбец отслеживания параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="930a0-172">Другой способ задания свойства отслеживания — текучий API.</span><span class="sxs-lookup"><span data-stu-id="930a0-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="930a0-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="930a0-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="930a0-174">Для базы данных SQL Server атрибут `[Timestamp]` свойства сущности, определенного как массив байтов, делает следующее:</span><span class="sxs-lookup"><span data-stu-id="930a0-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="930a0-175">приводит к тому, что столбец включается в предложения WHERE инструкций DELETE и UPDATE;</span><span class="sxs-lookup"><span data-stu-id="930a0-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="930a0-176">задает тип [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) для столбца в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="930a0-177">База данных формирует последовательный номер версии строки, увеличивающийся при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="930a0-178">В команде `Update` или `Delete` предложение `Where` включает в себя извлеченное значение версии строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="930a0-179">Если обновляемая строка изменились с момента получения:</span><span class="sxs-lookup"><span data-stu-id="930a0-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="930a0-180">текущее значение версии строки не соответствует полученному значению;</span><span class="sxs-lookup"><span data-stu-id="930a0-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="930a0-181">команда `Update` или `Delete` не находит строку, так как предложение `Where` ищет полученное значение версии строки;</span><span class="sxs-lookup"><span data-stu-id="930a0-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="930a0-182">Возникает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="930a0-183">В следующем коде показана часть кода T-SQL, созданного EF Core при обновлении названия кафедры:</span><span class="sxs-lookup"><span data-stu-id="930a0-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="930a0-184">Предыдущий выделенный код показывает предложение `WHERE`, содержащее `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="930a0-185">Если база данных `RowVersion` не соответствует параметру `RowVersion` (`@p2`), строки не обновляются.</span><span class="sxs-lookup"><span data-stu-id="930a0-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="930a0-186">Следующий выделенный код показывает код T-SQL, который подтверждает, что была обновлена всего одна строка.</span><span class="sxs-lookup"><span data-stu-id="930a0-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="930a0-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) возвращает число строк, затронутых при выполнении последнего оператора.</span><span class="sxs-lookup"><span data-stu-id="930a0-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="930a0-188">Если строки не обновляются, EF Core создает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="930a0-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="930a0-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="930a0-190">Для базы данных SQLite атрибут `[Timestamp]` свойства сущности, определенного как массив байтов, делает следующее:</span><span class="sxs-lookup"><span data-stu-id="930a0-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="930a0-191">приводит к тому, что столбец включается в предложения WHERE инструкций DELETE и UPDATE;</span><span class="sxs-lookup"><span data-stu-id="930a0-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="930a0-192">выполняет сопоставление с типом столбца больших двоичных объектов.</span><span class="sxs-lookup"><span data-stu-id="930a0-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="930a0-193">Триггеры базы данных обновляют столбец RowVersion новым случайным массивом байтов при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="930a0-194">В команде `Update` или `Delete` предложение `Where` включает в себя извлеченное значение столбца RowVersion.</span><span class="sxs-lookup"><span data-stu-id="930a0-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="930a0-195">Если обновляемая строка изменились с момента получения:</span><span class="sxs-lookup"><span data-stu-id="930a0-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="930a0-196">текущее значение версии строки не соответствует полученному значению;</span><span class="sxs-lookup"><span data-stu-id="930a0-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="930a0-197">команда `Update` или `Delete` не находит строку, так как предложение `Where` ищет исходное значение версии строки;</span><span class="sxs-lookup"><span data-stu-id="930a0-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="930a0-198">Возникает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="930a0-199">Обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="930a0-199">Update the database</span></span>

<span data-ttu-id="930a0-200">Добавление свойства `RowVersion` изменяет модель данных, которая требует миграции.</span><span class="sxs-lookup"><span data-stu-id="930a0-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="930a0-201">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="930a0-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="930a0-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="930a0-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="930a0-203">Выполните следующую команду в PMC:</span><span class="sxs-lookup"><span data-stu-id="930a0-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="930a0-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="930a0-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="930a0-205">В терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="930a0-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="930a0-206">Эта команда:</span><span class="sxs-lookup"><span data-stu-id="930a0-206">This command:</span></span>

* <span data-ttu-id="930a0-207">Создает файл миграции *Migrations/{метка времени}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="930a0-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="930a0-208">Изменяет файл *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="930a0-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="930a0-209">Это изменение добавляет следующий выделенный код в метод `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="930a0-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="930a0-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="930a0-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="930a0-211">Выполните следующую команду в PMC:</span><span class="sxs-lookup"><span data-stu-id="930a0-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="930a0-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="930a0-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="930a0-213">Откройте файл `Migrations/<timestamp>_RowVersion.cs` и добавьте в него выделенный код.</span><span class="sxs-lookup"><span data-stu-id="930a0-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="930a0-214">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-214">The preceding code:</span></span>

  * <span data-ttu-id="930a0-215">Обновляет существующие строки случайными значениями больших двоичных объектов.</span><span class="sxs-lookup"><span data-stu-id="930a0-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="930a0-216">Добавляет триггеры базы данных, которые присваивают столбцу RowVersion случайное значение большого двоичного объекта при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="930a0-217">В терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="930a0-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="930a0-218">Формирование шаблона страниц кафедр</span><span class="sxs-lookup"><span data-stu-id="930a0-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="930a0-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="930a0-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="930a0-220">Следуйте инструкциям в разделе [Формирование шаблона для страниц Student](xref:data/ef-rp/intro#scaffold-student-pages), за исключением следующего:</span><span class="sxs-lookup"><span data-stu-id="930a0-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="930a0-221">Создайте папку *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="930a0-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="930a0-222">Используйте класс модели `Department`.</span><span class="sxs-lookup"><span data-stu-id="930a0-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="930a0-223">Используйте существующий класс контекста вместо создания нового.</span><span class="sxs-lookup"><span data-stu-id="930a0-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="930a0-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="930a0-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="930a0-225">Создайте папку *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="930a0-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="930a0-226">Выполните приведенную ниже команду, чтобы сформировать шаблон для страниц кафедр.</span><span class="sxs-lookup"><span data-stu-id="930a0-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="930a0-227">**В Windows:**</span><span class="sxs-lookup"><span data-stu-id="930a0-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="930a0-228">**В Linux или macOS:**</span><span class="sxs-lookup"><span data-stu-id="930a0-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="930a0-229">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="930a0-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="930a0-230">Обновление страницы индекса</span><span class="sxs-lookup"><span data-stu-id="930a0-230">Update the Index page</span></span>

<span data-ttu-id="930a0-231">Средство формирования шаблонов создало столбец `RowVersion` для страницы индекса, однако это поле не будет отображаться в рабочем приложении.</span><span class="sxs-lookup"><span data-stu-id="930a0-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="930a0-232">В этом учебнике отображается последний байт `RowVersion` для демонстрации работы параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="930a0-233">Этот последний байт необязательно является уникальным.</span><span class="sxs-lookup"><span data-stu-id="930a0-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="930a0-234">Обновите страницу *Pages\Departments\Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="930a0-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="930a0-235">Замените Index на Departments.</span><span class="sxs-lookup"><span data-stu-id="930a0-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="930a0-236">Измените код, содержащий `RowVersion`, чтобы отображался только последний байт массива байтов.</span><span class="sxs-lookup"><span data-stu-id="930a0-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="930a0-237">Замените FirstMidName на FullName.</span><span class="sxs-lookup"><span data-stu-id="930a0-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="930a0-238">В следующем примере кода показана обновленная страница:</span><span class="sxs-lookup"><span data-stu-id="930a0-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="930a0-239">Обновление модели страницы "Edit" (Редактирование)</span><span class="sxs-lookup"><span data-stu-id="930a0-239">Update the Edit page model</span></span>

<span data-ttu-id="930a0-240">Измените *Pages\Departments\Edit.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="930a0-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> обновляется с помощью значения `rowVersion` из сущности при получении в методе `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="930a0-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="930a0-242">EF Core создает команду SQL UPDATE с предложением WHERE, содержащим исходное значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="930a0-243">Если команда UPDATE не затрагивает никакие строки (нет строк, имеющих исходное значение `RowVersion`), возникает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="930a0-244">В предыдущем выделенном коде:</span><span class="sxs-lookup"><span data-stu-id="930a0-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="930a0-245">Значение в `Department.RowVersion` — это значение, которое имела сущность, когда она была изначально получена в запросе Get для страницы редактирования.</span><span class="sxs-lookup"><span data-stu-id="930a0-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="930a0-246">Значение передается в метод `OnPost` посредством скрытого поля на странице Razor, на которой отображается редактируемая сущность.</span><span class="sxs-lookup"><span data-stu-id="930a0-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="930a0-247">Значение скрытого поля копируется в `Department.RowVersion` связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="930a0-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="930a0-248">EF Core будет использовать в предложении Where значение `OriginalValue`.</span><span class="sxs-lookup"><span data-stu-id="930a0-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="930a0-249">Перед выполнением выделенной строки кода `OriginalValue` имеет значение, которое было в базе данных при вызове `FirstOrDefaultAsync` в этом методе. Оно может отличаться от того, которое отображалось на странице редактирования.</span><span class="sxs-lookup"><span data-stu-id="930a0-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="930a0-250">Выделенный код гарантирует, что EF Core использует исходное значение `RowVersion` из отображаемой сущности `Department` в предложении WHERE инструкции SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="930a0-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="930a0-251">При возникновении ошибки параллелизма приведенный ниже выделенный код получает значения клиента (значения, переданные в этот метод) и значения из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="930a0-252">Следующий код добавляет пользовательское сообщение об ошибке для каждого столбца, у которого значения базы данных отличаются от переданных в `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="930a0-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="930a0-253">Приведенный ниже выделенный код задает для `RowVersion` новое значение, полученное из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="930a0-254">Когда пользователь в следующий раз нажимает кнопку **Save** (Сохранить), перехватываются только те ошибки параллелизма, возникшие с момента последнего отображения страницы "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="930a0-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="930a0-255">Оператор `ModelState.Remove` является обязательным, так как `ModelState` имеет старое значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="930a0-256">На странице Razor значение `ModelState` для поля имеет приоритет над значениями свойств модели, если они присутствуют вместе.</span><span class="sxs-lookup"><span data-stu-id="930a0-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="930a0-257">Обновление страницы редактирования</span><span class="sxs-lookup"><span data-stu-id="930a0-257">Update the Edit page</span></span>

<span data-ttu-id="930a0-258">Измените файл *Pages/Departments/Edit.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="930a0-259">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-259">The preceding code:</span></span>

* <span data-ttu-id="930a0-260">Изменяет директиву `page` с `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="930a0-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="930a0-261">Добавляет версию скрытых строк.</span><span class="sxs-lookup"><span data-stu-id="930a0-261">Adds a hidden row version.</span></span> <span data-ttu-id="930a0-262">Нужно добавить `RowVersion`, чтобы обратная передача привязывала значение.</span><span class="sxs-lookup"><span data-stu-id="930a0-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="930a0-263">Отображает последний байт `RowVersion` в целях отладки.</span><span class="sxs-lookup"><span data-stu-id="930a0-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="930a0-264">Заменяет `ViewData` на строго типизированный `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="930a0-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="930a0-265">Тестирование конфликтов параллелизма с использованием страницы "Edit" (Редактирование)</span><span class="sxs-lookup"><span data-stu-id="930a0-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="930a0-266">Откройте в браузере два экземпляра страницы "Edit" (Редактирование) для кафедры английского языка:</span><span class="sxs-lookup"><span data-stu-id="930a0-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="930a0-267">Запустите приложение и выберите "Departments" (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="930a0-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="930a0-268">Щелкните правой кнопкой мыши гиперссылку **Edit** (Изменить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**.</span><span class="sxs-lookup"><span data-stu-id="930a0-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="930a0-269">На первой вкладке щелкните гиперссылку **Edit** (Изменить) для кафедры английского языка.</span><span class="sxs-lookup"><span data-stu-id="930a0-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="930a0-270">На обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="930a0-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="930a0-271">Измените имя на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-271">Change the name in the first browser tab and click **Save**.</span></span>

![Страница "Edit" (Редактирование) кафедры 1 после изменения](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="930a0-273">В браузере отображается страница индекса с измененным значением и обновленным индикатором rowVersion.</span><span class="sxs-lookup"><span data-stu-id="930a0-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="930a0-274">Обратите внимание на обновленный индикатор rowVersion, он отображается при второй обратной передаче на другой вкладке.</span><span class="sxs-lookup"><span data-stu-id="930a0-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="930a0-275">Измените другое поле на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="930a0-275">Change a different field in the second browser tab.</span></span>

![Страница "Edit" (Редактирование) кафедры 2 после изменения](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="930a0-277">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="930a0-277">Click **Save**.</span></span> <span data-ttu-id="930a0-278">Отображаются сообщения об ошибках для всех полей, которые не соответствуют значениям базы данных:</span><span class="sxs-lookup"><span data-stu-id="930a0-278">You see error messages for all fields that don't match the database values:</span></span>

![Сообщение об ошибке для страницы "Edit" (Редактирование) кафедры](concurrency/_static/edit-error30.png)

<span data-ttu-id="930a0-280">В этом окне браузера не планировалось изменять поле "Name" (Имя).</span><span class="sxs-lookup"><span data-stu-id="930a0-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="930a0-281">Скопируйте и вставьте текущее значение "Languages" (Языки) в поле "Name" (Имя).</span><span class="sxs-lookup"><span data-stu-id="930a0-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="930a0-282">Выйдите из поля с помощью клавиши TAB. Проверка на стороне клиента удаляет сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="930a0-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="930a0-283">Снова нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-283">Click **Save** again.</span></span> <span data-ttu-id="930a0-284">Сохраняется значение, введенное на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="930a0-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="930a0-285">Сохраненные значения отображаются на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="930a0-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="930a0-286">Обновление модели страницы "Delete" (Удаление)</span><span class="sxs-lookup"><span data-stu-id="930a0-286">Update the Delete page model</span></span>

<span data-ttu-id="930a0-287">Измените файл *Pages/Departments/Delete.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="930a0-288">Страница "Delete" (Удаление) обнаруживает конфликты параллелизма при изменении сущности после ее получения.</span><span class="sxs-lookup"><span data-stu-id="930a0-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="930a0-289">`Department.RowVersion` является версией строки при получении сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="930a0-290">Когда EF Core создает команду SQL DELETE, она включает предложение WHERE с `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="930a0-291">Если команда SQL DELETE не затрагивает ни одной строки:</span><span class="sxs-lookup"><span data-stu-id="930a0-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="930a0-292">`RowVersion` в команде SQL DELETE не соответствует `RowVersion` в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="930a0-293">Возникает исключение DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="930a0-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="930a0-294">Вызывается `OnGetAsync` с `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="930a0-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="930a0-295">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="930a0-295">Update the Delete page</span></span>

<span data-ttu-id="930a0-296">Измените *Pages/Departments/Delete.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="930a0-297">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="930a0-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="930a0-298">Изменяет директиву `page` с `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="930a0-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="930a0-299">Добавляет сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="930a0-299">Adds an error message.</span></span>
* <span data-ttu-id="930a0-300">Заменяет FirstMidName на FullName в поле **Administrator** (Администратор).</span><span class="sxs-lookup"><span data-stu-id="930a0-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="930a0-301">Изменяет `RowVersion` для отображения последнего байта.</span><span class="sxs-lookup"><span data-stu-id="930a0-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="930a0-302">Добавляет версию скрытых строк.</span><span class="sxs-lookup"><span data-stu-id="930a0-302">Adds a hidden row version.</span></span> <span data-ttu-id="930a0-303">Нужно добавить `RowVersion`, чтобы обратная передача привязывала значение.</span><span class="sxs-lookup"><span data-stu-id="930a0-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="930a0-304">Тестирование конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="930a0-304">Test concurrency conflicts</span></span>

<span data-ttu-id="930a0-305">Создайте тестовую кафедру.</span><span class="sxs-lookup"><span data-stu-id="930a0-305">Create a test department.</span></span>

<span data-ttu-id="930a0-306">Откройте в браузере два экземпляра страницы "Delete" (Удаление) для тестовой кафедры:</span><span class="sxs-lookup"><span data-stu-id="930a0-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="930a0-307">Запустите приложение и выберите "Departments" (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="930a0-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="930a0-308">Щелкните правой кнопкой мыши гиперссылку **Delete** (Удалить) для тестовой кафедры и выберите пункт **Открыть на новой вкладке**.</span><span class="sxs-lookup"><span data-stu-id="930a0-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="930a0-309">Щелкните гиперссылку **Edit** (Изменить) для тестовой кафедры.</span><span class="sxs-lookup"><span data-stu-id="930a0-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="930a0-310">На обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="930a0-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="930a0-311">Измените бюджет на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="930a0-312">В браузере отображается страница индекса с измененным значением и обновленным индикатором rowVersion.</span><span class="sxs-lookup"><span data-stu-id="930a0-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="930a0-313">Обратите внимание на обновленный индикатор rowVersion, он отображается при второй обратной передаче на другой вкладке.</span><span class="sxs-lookup"><span data-stu-id="930a0-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="930a0-314">Удалите тестовую кафедру со второй закладки. Отображается ошибка параллелизма с текущими значениями из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="930a0-315">При нажатии кнопки **Delete** (Удалить) сущность удаляется, если только не был обновлен `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="930a0-316">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="930a0-316">Additional resources</span></span>

* [<span data-ttu-id="930a0-317">Токены параллелизма в EF Core</span><span class="sxs-lookup"><span data-stu-id="930a0-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="930a0-318">Обработка параллелизма в EF Core</span><span class="sxs-lookup"><span data-stu-id="930a0-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="930a0-319">Отладка источника ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="930a0-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="930a0-320">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="930a0-320">Next steps</span></span>

<span data-ttu-id="930a0-321">Это последний учебник из серии.</span><span class="sxs-lookup"><span data-stu-id="930a0-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="930a0-322">Дополнительные темы рассматриваются в [версии этой серии для MVC](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="930a0-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="930a0-323">Предыдущий учебник</span><span class="sxs-lookup"><span data-stu-id="930a0-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="930a0-324">Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей параллельно (одновременно) изменяют одну сущность.</span><span class="sxs-lookup"><span data-stu-id="930a0-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="930a0-325">При возникновении проблем, которые вам не удается устранить, [скачайте или просмотрите готовое приложение.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="930a0-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="930a0-326">[Указания по скачиванию](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="930a0-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="930a0-327">Конфликты параллелизма</span><span class="sxs-lookup"><span data-stu-id="930a0-327">Concurrency conflicts</span></span>

<span data-ttu-id="930a0-328">Конфликт параллелизма возникает в следующих условиях:</span><span class="sxs-lookup"><span data-stu-id="930a0-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="930a0-329">Пользователь переходит на страницу редактирования для сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="930a0-330">Другой пользователь обновляет ту же сущность до того, как изменение первого пользователя записывается в базу данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="930a0-331">Если обнаружение параллелизма отключено, возникают параллельные изменения:</span><span class="sxs-lookup"><span data-stu-id="930a0-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="930a0-332">Побеждает последнее изменение.</span><span class="sxs-lookup"><span data-stu-id="930a0-332">The last update wins.</span></span> <span data-ttu-id="930a0-333">Таким образом, в базу данных заносятся значения из последнего изменения.</span><span class="sxs-lookup"><span data-stu-id="930a0-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="930a0-334">Первые из текущих изменений утрачиваются.</span><span class="sxs-lookup"><span data-stu-id="930a0-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="930a0-335">Оптимистическая блокировка</span><span class="sxs-lookup"><span data-stu-id="930a0-335">Optimistic concurrency</span></span>

<span data-ttu-id="930a0-336">Оптимистическая блокировка допускает появление конфликтов параллелизма, а затем обрабатывает их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="930a0-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="930a0-337">Например, Мария посещает страницу изменения кафедры и изменяет бюджет кафедры английской языка с 350 000,00 USD на 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Изменение бюджета на 0](concurrency/_static/change-budget.png)

<span data-ttu-id="930a0-339">Прежде чем Мария нажимает кнопку **Save** (Сохранить), Дмитрий заходит на ту же страницу и изменяет значение в поле "Start Date" (Дата начала) с 9/1/2007 на 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="930a0-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Изменение начальной даты на 2013 г.](concurrency/_static/change-date.png)

<span data-ttu-id="930a0-341">Сначала Мария нажимает кнопку **Save** (Сохранить) и видит свое изменение, когда браузер отображает страницу индекса.</span><span class="sxs-lookup"><span data-stu-id="930a0-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Бюджет изменен на нуль](concurrency/_static/budget-zero.png)

<span data-ttu-id="930a0-343">Дмитрий нажимает кнопку **Save** (Сохранить) на странице редактирования, где все еще отображается бюджет 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="930a0-344">Дальнейший ход событий определяется порядком обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="930a0-345">Оптимистическая блокировка включает в себя следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="930a0-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="930a0-346">Вы можете отслеживать, для какого свойства пользователь изменил и обновил только соответствующие столбцы в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="930a0-347">В этом сценарии никакие данные не теряются.</span><span class="sxs-lookup"><span data-stu-id="930a0-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="930a0-348">Разные свойства были обновлены двумя пользователями.</span><span class="sxs-lookup"><span data-stu-id="930a0-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="930a0-349">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит изменения, внесенные как Марией, так и Дмитрием.</span><span class="sxs-lookup"><span data-stu-id="930a0-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="930a0-350">Этот способ обновления позволяет снизить количество конфликтов, способных привести к потере данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="930a0-351">Особенности этого подхода:</span><span class="sxs-lookup"><span data-stu-id="930a0-351">This approach:</span></span>
 
  * <span data-ttu-id="930a0-352">Он не позволяет избежать потери данных, если конкурирующие изменения вносятся для одного свойства.</span><span class="sxs-lookup"><span data-stu-id="930a0-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="930a0-353">Он не слишком хорошо подходит для веб-приложений,</span><span class="sxs-lookup"><span data-stu-id="930a0-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="930a0-354">так как требует поддерживать значительный объем состояний, чтобы отслеживать все извлеченные и новые значения.</span><span class="sxs-lookup"><span data-stu-id="930a0-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="930a0-355">Обслуживание большого объема состояний может негативно повлиять на производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="930a0-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="930a0-356">Он может повысить сложность приложений по сравнению с обнаружением параллелизма для сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="930a0-357">Вы можете позволить изменению Дмитрия перезаписать изменение Марии.</span><span class="sxs-lookup"><span data-stu-id="930a0-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="930a0-358">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит дату 9/1/2013 и значение 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="930a0-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="930a0-359">Такой подход называется *победой клиента* или *сохранением последнего внесенного изменения*.</span><span class="sxs-lookup"><span data-stu-id="930a0-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="930a0-360">(Все значения из клиента имеют приоритет над данными в хранилище.) Если не писать код для обработки параллелизма, автоматически применяется победа клиента.</span><span class="sxs-lookup"><span data-stu-id="930a0-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="930a0-361">Вы можете запретить обновление изменения Дмитрия в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="930a0-362">Как правило приложение будет:</span><span class="sxs-lookup"><span data-stu-id="930a0-362">Typically, the app would:</span></span>

  * <span data-ttu-id="930a0-363">отображать сообщение об ошибке;</span><span class="sxs-lookup"><span data-stu-id="930a0-363">Display an error message.</span></span>
  * <span data-ttu-id="930a0-364">отображать текущее состояние данных;</span><span class="sxs-lookup"><span data-stu-id="930a0-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="930a0-365">разрешать пользователю повторно применять изменения.</span><span class="sxs-lookup"><span data-stu-id="930a0-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="930a0-366">Это называется *победой хранилища*.</span><span class="sxs-lookup"><span data-stu-id="930a0-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="930a0-367">(Значения в хранилище имеют приоритет над данными, передаваемыми клиентом.) В этом руководстве вы реализуете сценарий победы хранилища.</span><span class="sxs-lookup"><span data-stu-id="930a0-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="930a0-368">Данный метод гарантирует, что никакие изменения не перезаписываются без оповещения пользователя.</span><span class="sxs-lookup"><span data-stu-id="930a0-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="930a0-369">Обработка параллелизма</span><span class="sxs-lookup"><span data-stu-id="930a0-369">Handling concurrency</span></span> 

<span data-ttu-id="930a0-370">Если свойство настроено как [токен параллелизма](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="930a0-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="930a0-371">EF Core проверяет, что свойство не было изменено после его получения.</span><span class="sxs-lookup"><span data-stu-id="930a0-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="930a0-372">Эта проверка выполняется при вызове [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) или [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="930a0-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="930a0-373">Если свойство было изменено после получения, возникает исключение [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception).</span><span class="sxs-lookup"><span data-stu-id="930a0-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="930a0-374">Нужно настроить базу данных и модель данных для поддержки исключения `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="930a0-375">Обнаружение конфликтов параллелизма для свойства</span><span class="sxs-lookup"><span data-stu-id="930a0-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="930a0-376">Конфликты параллелизма можно обнаружить на уровне свойств с помощью атрибута [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute).</span><span class="sxs-lookup"><span data-stu-id="930a0-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="930a0-377">Этот атрибут можно применить к нескольким свойствам в модели.</span><span class="sxs-lookup"><span data-stu-id="930a0-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="930a0-378">Дополнительные сведения см. в [описании ConcurrencyCheck в подразделе "Заметки к данным"](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="930a0-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="930a0-379">Атрибут`[ConcurrencyCheck]` в этом руководстве не используется.</span><span class="sxs-lookup"><span data-stu-id="930a0-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="930a0-380">Обнаружение конфликтов параллелизма для строки</span><span class="sxs-lookup"><span data-stu-id="930a0-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="930a0-381">Чтобы обнаружить конфликты параллелизма, в модель добавлен столбец отслеживания [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="930a0-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="930a0-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="930a0-382">`rowversion` :</span></span>

* <span data-ttu-id="930a0-383">Относится к SQL Server.</span><span class="sxs-lookup"><span data-stu-id="930a0-383">Is SQL Server specific.</span></span> <span data-ttu-id="930a0-384">Другие базы данных могут не предоставлять подобную функцию.</span><span class="sxs-lookup"><span data-stu-id="930a0-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="930a0-385">Используется для определения того, что сущность не была изменена после ее получения из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="930a0-386">База данных формирует последовательный номер `rowversion`, увеличивающийся при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="930a0-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="930a0-387">В команде `Update` или `Delete` предложение `Where` включает извлеченное значение из `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="930a0-388">Если обновляемая строка изменились:</span><span class="sxs-lookup"><span data-stu-id="930a0-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="930a0-389">`rowversion` не соответствует полученному значению.</span><span class="sxs-lookup"><span data-stu-id="930a0-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="930a0-390">Команда `Update` или `Delete` не находит строку, так как предложение `Where` включает полученное значение `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="930a0-391">Возникает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="930a0-392">Когда в EF Core нет строк, обновленных командой `Update` или `Delete`, возникает исключение параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="930a0-393">Добавление свойства отслеживания в сущность Department</span><span class="sxs-lookup"><span data-stu-id="930a0-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="930a0-394">В *Models/Department.cs* добавьте свойство отслеживания RowVersion:</span><span class="sxs-lookup"><span data-stu-id="930a0-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="930a0-395">Атрибут [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) указывает, что этот столбец входит в предложение `Where` для команд `Update` и `Delete`.</span><span class="sxs-lookup"><span data-stu-id="930a0-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="930a0-396">Этот атрибут называется `Timestamp`, так как предыдущие версии SQL Server использовали тип данных `timestamp` SQL, пока его не сменил тип `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="930a0-397">Текучий API также может задавать свойство отслеживания:</span><span class="sxs-lookup"><span data-stu-id="930a0-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="930a0-398">В следующем коде показана часть кода T-SQL, созданного EF Core при обновлении названия кафедры:</span><span class="sxs-lookup"><span data-stu-id="930a0-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="930a0-399">Предыдущий выделенный код показывает предложение `WHERE`, содержащее `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="930a0-400">Если база данных `RowVersion` не соответствует параметру `RowVersion` (`@p2`), никакие строки не обновляются.</span><span class="sxs-lookup"><span data-stu-id="930a0-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="930a0-401">Следующий выделенный код показывает код T-SQL, который подтверждает, что была обновлена всего одна строка.</span><span class="sxs-lookup"><span data-stu-id="930a0-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="930a0-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) возвращает число строк, затронутых при выполнении последнего оператора.</span><span class="sxs-lookup"><span data-stu-id="930a0-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="930a0-403">Если никакие строки не обновляются, EF Core выдает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="930a0-404">Вы можете просмотреть код T-SQL, создаваемый EF Core в окне вывода Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="930a0-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="930a0-405">Обновление базы данных</span><span class="sxs-lookup"><span data-stu-id="930a0-405">Update the DB</span></span>

<span data-ttu-id="930a0-406">Добавление свойства `RowVersion` изменяет модель базы данных, которая требует миграции.</span><span class="sxs-lookup"><span data-stu-id="930a0-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="930a0-407">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="930a0-407">Build the project.</span></span> <span data-ttu-id="930a0-408">Введите в командном окне следующее:</span><span class="sxs-lookup"><span data-stu-id="930a0-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="930a0-409">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="930a0-409">The preceding commands:</span></span>

* <span data-ttu-id="930a0-410">Добавляет файл миграций *Migrations/{метка времени}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="930a0-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="930a0-411">Изменяет файл *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="930a0-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="930a0-412">Это изменение добавляет следующий выделенный код в метод `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="930a0-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="930a0-413">Запускает миграции для обновления базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="930a0-414">Формирование шаблона для модели кафедр</span><span class="sxs-lookup"><span data-stu-id="930a0-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="930a0-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="930a0-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="930a0-416">Следуйте инструкциям в разделе [Формирование шаблона для модели Student](xref:data/ef-rp/intro#scaffold-student-pages) и используйте `Department` для класса модели.</span><span class="sxs-lookup"><span data-stu-id="930a0-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="930a0-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="930a0-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="930a0-418">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="930a0-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="930a0-419">Предыдущая команда формирует шаблон для модели `Department`.</span><span class="sxs-lookup"><span data-stu-id="930a0-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="930a0-420">Откройте проект в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="930a0-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="930a0-421">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="930a0-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="930a0-422">Изменение страницы индекса кафедр</span><span class="sxs-lookup"><span data-stu-id="930a0-422">Update the Departments Index page</span></span>

<span data-ttu-id="930a0-423">Подсистема формирования шаблонов создала столбец `RowVersion` для страницы индекса, однако это поле не должно отображаться.</span><span class="sxs-lookup"><span data-stu-id="930a0-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="930a0-424">В этом руководстве отображается последний байт `RowVersion` для лучшего понимания параллелизма.</span><span class="sxs-lookup"><span data-stu-id="930a0-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="930a0-425">Этот последний байт необязательно является уникальным.</span><span class="sxs-lookup"><span data-stu-id="930a0-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="930a0-426">Реальное приложение не будет отображать `RowVersion` или последний байт `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="930a0-427">Обновите страницу индекса:</span><span class="sxs-lookup"><span data-stu-id="930a0-427">Update the Index page:</span></span>

* <span data-ttu-id="930a0-428">Замените Index на Departments.</span><span class="sxs-lookup"><span data-stu-id="930a0-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="930a0-429">Замените разметку, содержащую `RowVersion`, на последний байт `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="930a0-430">Замените FirstMidName на FullName.</span><span class="sxs-lookup"><span data-stu-id="930a0-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="930a0-431">Следующая разметка показывает обновленную страницу:</span><span class="sxs-lookup"><span data-stu-id="930a0-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="930a0-432">Обновление модели страницы "Edit" (Редактирование)</span><span class="sxs-lookup"><span data-stu-id="930a0-432">Update the Edit page model</span></span>

<span data-ttu-id="930a0-433">Измените *Pages\Departments\Edit.cshtml.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="930a0-434">Для обнаружения проблемы параллелизма [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) обновляется с помощью значения `rowVersion` из сущности, откуда он был получен.</span><span class="sxs-lookup"><span data-stu-id="930a0-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="930a0-435">EF Core создает команду SQL UPDATE с предложением WHERE, содержащим исходное значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="930a0-436">Если команда UPDATE не затрагивает никакие строки (нет строк, имеющих исходное значение `RowVersion`), возникает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="930a0-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="930a0-437">В приведенном выше коде `Department.RowVersion` является значением на момент извлечения сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="930a0-438">`OriginalValue` является значением в базе данных на момент вызова `FirstOrDefaultAsync` в этом методе.</span><span class="sxs-lookup"><span data-stu-id="930a0-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="930a0-439">Следующий код возвращает значения клиента (значения, переданные в этот метод) и значения базы данных:</span><span class="sxs-lookup"><span data-stu-id="930a0-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="930a0-440">Следующий код добавляет пользовательское сообщение об ошибке для каждого столбца, у которого значения базы данных отличаются в переданных в `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="930a0-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="930a0-441">Следующий выделенный код задает для `RowVersion` новое значение, полученное из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="930a0-442">Когда пользователь в следующий раз нажимает кнопку **Save** (Сохранить), перехватываются только те ошибки параллелизма, возникшие с момента последнего отображения страницы "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="930a0-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="930a0-443">Оператор `ModelState.Remove` является обязательным, так как `ModelState` имеет старое значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="930a0-444">На странице Razor значение `ModelState` для поля имеет приоритет над значениями свойств модели, если они присутствуют вместе.</span><span class="sxs-lookup"><span data-stu-id="930a0-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="930a0-445">Обновление страницы редактирования</span><span class="sxs-lookup"><span data-stu-id="930a0-445">Update the Edit page</span></span>

<span data-ttu-id="930a0-446">Обновите *Pages/Departments/Edit.cshtml*, используя следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="930a0-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="930a0-447">Предыдущая разметка:</span><span class="sxs-lookup"><span data-stu-id="930a0-447">The preceding markup:</span></span>

* <span data-ttu-id="930a0-448">Изменяет директиву `page` с `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="930a0-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="930a0-449">Добавляет версию скрытых строк.</span><span class="sxs-lookup"><span data-stu-id="930a0-449">Adds a hidden row version.</span></span> <span data-ttu-id="930a0-450">Нужно добавить `RowVersion`, чтобы обратная передача привязывала значение.</span><span class="sxs-lookup"><span data-stu-id="930a0-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="930a0-451">Отображает последний байт `RowVersion` в целях отладки.</span><span class="sxs-lookup"><span data-stu-id="930a0-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="930a0-452">Заменяет `ViewData` на строго типизированный `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="930a0-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="930a0-453">Тестирование конфликтов параллелизма с использованием страницы "Edit" (Редактирование)</span><span class="sxs-lookup"><span data-stu-id="930a0-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="930a0-454">Откройте в браузере два экземпляра страницы "Edit" (Редактирование) для кафедры английского языка:</span><span class="sxs-lookup"><span data-stu-id="930a0-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="930a0-455">Запустите приложение и выберите "Departments" (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="930a0-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="930a0-456">Щелкните правой кнопкой мыши гиперссылку **Edit** (Изменить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**.</span><span class="sxs-lookup"><span data-stu-id="930a0-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="930a0-457">На первой вкладке щелкните гиперссылку **Edit** (Изменить) для кафедры английского языка.</span><span class="sxs-lookup"><span data-stu-id="930a0-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="930a0-458">На обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="930a0-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="930a0-459">Измените имя на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-459">Change the name in the first browser tab and click **Save**.</span></span>

![Страница "Edit" (Редактирование) кафедры 1 после изменения](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="930a0-461">В браузере отображается страница индекса с измененным значением и обновленным индикатором rowVersion.</span><span class="sxs-lookup"><span data-stu-id="930a0-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="930a0-462">Обратите внимание на обновленный индикатор rowVersion, он отображается при второй обратной передаче на другой вкладке.</span><span class="sxs-lookup"><span data-stu-id="930a0-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="930a0-463">Измените другое поле на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="930a0-463">Change a different field in the second browser tab.</span></span>

![Страница "Edit" (Редактирование) кафедры 2 после изменения](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="930a0-465">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="930a0-465">Click **Save**.</span></span> <span data-ttu-id="930a0-466">Отображаются сообщения об ошибках для всех полей, которые не соответствуют значениям базы данных:</span><span class="sxs-lookup"><span data-stu-id="930a0-466">You see error messages for all fields that don't match the DB values:</span></span>

![Сообщение об ошибке для страницы "Edit" (Редактирование) кафедры](concurrency/_static/edit-error.png)

<span data-ttu-id="930a0-468">В этом окне браузера не планировалось изменять поле "Name" (Имя).</span><span class="sxs-lookup"><span data-stu-id="930a0-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="930a0-469">Скопируйте и вставьте текущее значение "Languages" (Языки) в поле "Name" (Имя).</span><span class="sxs-lookup"><span data-stu-id="930a0-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="930a0-470">Выйдите из поля с помощью клавиши TAB. Проверка на стороне клиента удаляет сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="930a0-470">Tab out. Client-side validation removes the error message.</span></span>

![Сообщение об ошибке для страницы "Edit" (Редактирование) кафедры](concurrency/_static/cv.png)

<span data-ttu-id="930a0-472">Снова нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-472">Click **Save** again.</span></span> <span data-ttu-id="930a0-473">Сохраняется значение, введенное на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="930a0-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="930a0-474">Сохраненные значения отображаются на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="930a0-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="930a0-475">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="930a0-475">Update the Delete page</span></span>

<span data-ttu-id="930a0-476">Обновите страницу "Delete" (Удаление) с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="930a0-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="930a0-477">Страница "Delete" (Удаление) обнаруживает конфликты параллелизма при изменении сущности после ее получения.</span><span class="sxs-lookup"><span data-stu-id="930a0-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="930a0-478">`Department.RowVersion` является версией строки при получении сущности.</span><span class="sxs-lookup"><span data-stu-id="930a0-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="930a0-479">Когда EF Core создает команду SQL DELETE, она включает предложение WHERE с `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="930a0-480">Если команда SQL DELETE не затрагивает ни одной строки:</span><span class="sxs-lookup"><span data-stu-id="930a0-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="930a0-481">`RowVersion` в команде SQL DELETE не соответствует `RowVersion` в базе данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="930a0-482">Возникает исключение DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="930a0-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="930a0-483">Вызывается `OnGetAsync` с `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="930a0-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="930a0-484">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="930a0-484">Update the Delete page</span></span>

<span data-ttu-id="930a0-485">Измените *Pages/Departments/Delete.cshtml*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="930a0-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="930a0-486">Приведенный выше код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="930a0-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="930a0-487">Изменяет директиву `page` с `@page` на `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="930a0-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="930a0-488">Добавляет сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="930a0-488">Adds an error message.</span></span>
* <span data-ttu-id="930a0-489">Заменяет FirstMidName на FullName в поле **Administrator** (Администратор).</span><span class="sxs-lookup"><span data-stu-id="930a0-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="930a0-490">Изменяет `RowVersion` для отображения последнего байта.</span><span class="sxs-lookup"><span data-stu-id="930a0-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="930a0-491">Добавляет версию скрытых строк.</span><span class="sxs-lookup"><span data-stu-id="930a0-491">Adds a hidden row version.</span></span> <span data-ttu-id="930a0-492">Нужно добавить `RowVersion`, чтобы обратная передача привязывала значение.</span><span class="sxs-lookup"><span data-stu-id="930a0-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="930a0-493">Тестирование конфликтов параллелизма с использованием страницы "Delete" (Удаление)</span><span class="sxs-lookup"><span data-stu-id="930a0-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="930a0-494">Создайте тестовую кафедру.</span><span class="sxs-lookup"><span data-stu-id="930a0-494">Create a test department.</span></span>

<span data-ttu-id="930a0-495">Откройте в браузере два экземпляра страницы "Delete" (Удаление) для тестовой кафедры:</span><span class="sxs-lookup"><span data-stu-id="930a0-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="930a0-496">Запустите приложение и выберите "Departments" (Кафедры).</span><span class="sxs-lookup"><span data-stu-id="930a0-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="930a0-497">Щелкните правой кнопкой мыши гиперссылку **Delete** (Удалить) для тестовой кафедры и выберите пункт **Открыть на новой вкладке**.</span><span class="sxs-lookup"><span data-stu-id="930a0-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="930a0-498">Щелкните гиперссылку **Edit** (Изменить) для тестовой кафедры.</span><span class="sxs-lookup"><span data-stu-id="930a0-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="930a0-499">На обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="930a0-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="930a0-500">Измените бюджет на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="930a0-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="930a0-501">В браузере отображается страница индекса с измененным значением и обновленным индикатором rowVersion.</span><span class="sxs-lookup"><span data-stu-id="930a0-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="930a0-502">Обратите внимание на обновленный индикатор rowVersion, он отображается при второй обратной передаче на другой вкладке.</span><span class="sxs-lookup"><span data-stu-id="930a0-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="930a0-503">Удалите тестовую кафедру со второй закладки. Отображается ошибка параллелизма с текущими значениями из базы данных.</span><span class="sxs-lookup"><span data-stu-id="930a0-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="930a0-504">При нажатии кнопки **Delete** (Удалить) сущность удаляется, если только не был обновлен `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="930a0-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="930a0-505">Сведения о том, как наследовать модель данных, см. в разделе [Наследование](xref:data/ef-mvc/inheritance).</span><span class="sxs-lookup"><span data-stu-id="930a0-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="930a0-506">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="930a0-506">Additional resources</span></span>

* [<span data-ttu-id="930a0-507">Токены параллелизма в EF Core</span><span class="sxs-lookup"><span data-stu-id="930a0-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="930a0-508">Обработка параллелизма в EF Core</span><span class="sxs-lookup"><span data-stu-id="930a0-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="930a0-509">Версия руководства на YouTube (обработка конфликтов параллелизма)</span><span class="sxs-lookup"><span data-stu-id="930a0-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="930a0-510">Версия руководства на YouTube (часть 2)</span><span class="sxs-lookup"><span data-stu-id="930a0-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="930a0-511">Версия руководства на YouTube (часть 3)</span><span class="sxs-lookup"><span data-stu-id="930a0-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="930a0-512">Назад</span><span class="sxs-lookup"><span data-stu-id="930a0-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

