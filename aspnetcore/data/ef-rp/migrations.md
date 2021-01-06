---
title: Часть 4. Razor Pages с EF Core в ASP.NET Core — миграции
author: rick-anderson
description: Часть 4 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061110"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Часть 4. Миграции Razor Pages с EF Core в ASP.NET Core

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Йон П. Смит](https://twitter.com/thereformedprog) (Jon P Smith) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

В этом учебнике представлена функция миграций EF Core для управления изменениями модели данных.

При разработке нового приложения модель данных часто изменяется. При каждом изменении нарушается синхронизация модели с базой данных. Вы начали работу с этой серией учебников с настройки платформы Entity Framework для создания базы данных, если она еще не существует. Каждый раз при изменении модели данных необходимо удалять базу данных. При следующем запуске приложения вызов `EnsureCreated` повторно создает базу данных в соответствии с новой моделью данных. Затем выполняется класс `DbInitializer` для заполнения новой базы данных.

Этот подход к обеспечению синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде. Приложение, выполняющееся в рабочей среде, обычно содержит данные. Приложение не может запускаться с тестовой базой данных каждый раз при внесении изменений (например, при добавлении столбца). Функция миграций EF Core решает эту проблему, позволяя EF Core обновить схему базы данных вместо создания базы данных.

Вместо удаления и повторного создания базы данных при изменении модели функция миграций обновляет схему, сохраняя существующие данные.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Удаление базы данных

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Используйте **обозреватель объектов SQL Server**, чтобы удалить базу данных, или выполните следующую команду в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Чтобы установить CLI EF, выполните в командной строке следующую команду:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* В командной строке перейдите к папке проекта. Папка проекта содержит файл *ContosoUniversity.csproj*.

* Удалите файл *CU.db* или выполните следующую команду:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Создание первоначальной миграции

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующие команды в PMC:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Убедитесь в том, что в командной строке выбрана папка проекта, и выполните следующие команды:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Методы Up и Down

Команда EF Core `migrations add` создала код для создания базы данных. Код миграции находится в файле *Migrations\<timestamp>_InitialCreate.cs*. Метод `Up` класса `InitialCreate` создает таблицы базы данных, соответствующие наборам сущностей модели данных. Метод `Down` удаляет их, как показано в следующем примере:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Приведенный выше код предназначен для первоначальной миграции. Код.

* был создан командой `migrations add InitialCreate`; 
* выполняется командой `database update`;
* создает базу данных для модели данных, заданной классом контекста базы данных.

Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла. В качестве имени миграции можно использовать любое допустимое имя файла. Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции. Например, миграция, обеспечивающая добавление таблицы кафедр, может называться "AddDepartmentTable".

## <a name="the-migrations-history-table"></a>Таблица журнала миграции

* Используйте обозреватель объектов SQL Server или средство SQLite для просмотра базы данных.
* Обратите внимание на добавление таблицы `__EFMigrationsHistory`. Таблица `__EFMigrationsHistory` используется для отслеживания миграций, которые были применены к базе данных.
* Просмотрите данные в таблице `__EFMigrationsHistory`. Вы увидите одну строку для первой миграции.

## <a name="the-data-model-snapshot"></a>Моментальный снимок модели данных

Функция миграций создает *моментальный снимок* текущей модели данных в файле *Migrations/SchoolContextModelSnapshot.cs*. При добавлении миграции EF определяет, что именно изменилось, сравнивая текущую модель данных с файлом моментального снимка.

Так как файл моментального снимка отслеживает состояние модели данных, вы не можете удалить миграцию, удалив файл `<timestamp>_<migrationname>.cs`. Чтобы отменить последнюю миграцию, необходимо использовать команду `migrations remove`. Она удаляет миграцию и гарантирует корректный сброс моментального снимка. Дополнительные сведения см. в разделе [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Удаление EnsureCreated

Эта серия учебников началась с использования `EnsureCreated`. Метод `EnsureCreated` не создает таблицу журнала миграций и поэтому не может использоваться с функцией миграций. Он предназначен для тестирования или быстрого создания прототипов, когда часто требуется удалять и повторно создавать базу данных.

Начиная с этого момента в учебниках будут использоваться миграции.

В файле *Data/DBInitializer.cs* закомментируйте следующую строку:

```csharp
context.Database.EnsureCreated();
```
Запустите приложение и убедитесь в том, что база данных заполняется.

## <a name="applying-migrations-in-production"></a>Применение миграций в рабочей среде

Для рабочих приложений **не рекомендуется** вызывать [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) при запуске приложения. `Migrate` не следует вызывать из приложения, развернутого в ферме серверов. Если приложение масштабируется в нескольких экземплярах сервера, трудно гарантировать, что изменения схемы базы данных не будут выполняться с нескольких серверов и не будут конфликтовать с обращениями для чтения или записи.

Миграция базы данных должна выполняться контролируемым способом в рамках развертывания. Подход к миграции рабочей базы данных включает следующее:

* Использование миграций для создания сценариев SQL и их использования в развертывании.
* Выполнение `dotnet ef database update` из контролируемой среды.

## <a name="troubleshooting"></a>Устранение неполадок

Если приложение использует SQL Server LocalDB и выводит следующее исключение:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

решением может быть выполнение `dotnet ef database update` из командной строки.

### <a name="additional-resources"></a>Дополнительные ресурсы

* [Интерфейс командной строки EF Core](/ef/core/miscellaneous/cli/dotnet).
* [Консоль диспетчера пакетов (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Следующие шаги

В следующем руководстве продолжается построение модели данных путем добавления свойств сущностей и новых сущностей.

> [!div class="step-by-step"]
> [Предыдущий учебник](xref:data/ef-rp/sort-filter-page)
> [Следующий учебник](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом учебнике используется функция миграций EF Core для управления изменениями модели данных.

При возникновении проблем, которые вам не удается устранить, скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

При разработке нового приложения модель данных часто изменяется. При каждом изменении нарушается синхронизация модели с базой данных. Вы начали работу с этим учебником, настроив платформу Entity Framework для создания базы данных, если она еще не существует. Каждый раз при изменении модели данных:

* База данных удаляется.
* Платформа EF создает новую базу данных, соответствующую модели.
* Приложение заполняет базу тестовыми данными.

Этот подход к обеспечению синхронизации базы данных с моделью данных хорошо работает до развертывания приложения в рабочей среде. Приложение, выполняющееся в рабочей среде, обычно содержит данные. Приложение не может запускаться с тестовой базой данных каждый раз при внесении изменений (например, при добавлении столбца). Функция миграций EF Core решает эту проблему, позволяя платформе EF Core обновить схему базы данных вместо создания новой базы.

Вместо удаления и повторного создания базы данных при изменении модели функция миграций обновляет схему, сохраняя существующие данные.

## <a name="drop-the-database"></a>Удаление базы данных

Воспользуйтесь **обозревателем объектов SQL Server** (SSOX) или командой `database drop`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующие команды в **консоли диспетчера пакетов** (PMC):

```powershell
Drop-Database
```

Чтобы просмотреть справочную информацию, выполните команду `Get-Help about_EntityFrameworkCore` в PMC.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Откройте командное окно и перейдите в папку проекта. Папка проекта содержит файл *Startup.cs*.

Введите в командном окне следующее:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Создание первоначальной миграции и обновление базы данных

Выполните построение проекта и создайте первую миграцию.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Обзор методов Up и Down

Команда EF Core `migrations add` создала код для создания базы данных. Код миграции находится в файле *Migrations\<timestamp>_InitialCreate.cs*. Метод `Up` класса `InitialCreate` создает таблицы базы данных, соответствующие наборам сущностей модели данных. Метод `Down` удаляет их, как показано в следующем примере:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Функция миграций вызывает метод `Up`, чтобы реализовать изменения модели данных для миграции. При вводе команды для отката обновления функция миграций вызывает метод `Down`.

Приведенный выше код предназначен для первоначальной миграции. Этот код был создан при выполнении команды `migrations add InitialCreate`. Параметр имени миграции (в примере это "InitialCreate") используется в качестве имени файла. В качестве имени миграции можно использовать любое допустимое имя файла. Рекомендуется выбрать слово или фразу, которые кратко описывают назначение миграции. Например, миграция, обеспечивающая добавление таблицы кафедр, может называться "AddDepartmentTable".

Если создается первоначальная миграция и база данных существует:

* Формируется код создания базы данных.
* Выполнять код создания базы данных не нужно, поскольку база уже соответствует модели данных. В случае выполнения код создания базы данных не вносит никаких изменений, поскольку база уже соответствует модели данных.

При развертывании приложения в новой среде этот код необходимо выполнить для создания новой базы данных.

Предыдущая база данных была удалена и не существует, поэтому функция миграций создает новую базу данных.

### <a name="the-data-model-snapshot"></a>Моментальный снимок модели данных

Функция миграций создает *моментальный снимок* текущей схемы базы данных в *Migrations/SchoolContextModelSnapshot.cs*. При добавлении миграции EF определяет, что именно изменилось, сравнивая модель данных с файлом моментального снимка.

Чтобы удалить миграцию, выполните следующую команду:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Дополнительные сведения см. в разделе [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Команда remove migrations удаляет миграцию и гарантирует корректный сброс моментального снимка.

### <a name="remove-ensurecreated-and-test-the-app"></a>Удаление EnsureCreated и тестирование приложения

Для ранней разработки использовалась команда `EnsureCreated`. В этом учебнике используются миграции. `EnsureCreated` имеет следующие ограничения:

* Пропускает миграции и создает базу данных и схему.
* Не создает таблицу миграций.
* *Не может* использоваться с миграциями.
* Разработана для тестирования или быстрого создания прототипов, когда часто требуется удаление и повторное создание базы данных.

Удалите `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Запустите приложение и убедитесь, что база заполняется данными.

### <a name="inspect-the-database"></a>Проверка базы данных

Используйте **обозреватель объектов SQL Server** для проверки базы данных. Обратите внимание на добавление таблицы `__EFMigrationsHistory`. Таблица `__EFMigrationsHistory` используется для отслеживания миграций, которые были применены к базе данных. Просмотрев данные в таблице `__EFMigrationsHistory`, вы увидите одну строку для первой миграции. Последний журнал в предыдущем примере выходных данных интерфейса командной строки показывает инструкцию INSERT, создающую эту строку.

Запустите приложение и убедитесь, что все функции работают.

## <a name="applying-migrations-in-production"></a>Применение миграций в рабочей среде

Для рабочих приложений **не рекомендуется** вызывать [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) при запуске приложения. `Migrate` не следует вызывать из приложения в ферме серверов. Например, если приложение было развернуто в облаке с горизонтальным масштабированием (выполняется несколько экземпляров приложения).

Миграция базы данных должна выполняться контролируемым способом в рамках развертывания. Подход к миграции рабочей базы данных включает следующее:

* Использование миграций для создания сценариев SQL и их использования в развертывании.
* Выполнение `dotnet ef database update` из контролируемой среды.

Платформа EF Core использует таблицу `__MigrationsHistory` для определения необходимости выполнить какие-либо миграции. Если база данных находится в актуальном состоянии, миграция не выполняется.

## <a name="troubleshooting"></a>Устранение неполадок

Скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

Приложение создает следующее исключение:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Решение: Выполнить `dotnet ef database update`

### <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [Интерфейс командной строки .NET Core](/ef/core/miscellaneous/cli/dotnet).
* [Консоль диспетчера пакетов (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Назад](xref:data/ef-rp/sort-filter-page)
> [Вперед](xref:data/ef-rp/complex-data-model)

::: moniker-end

