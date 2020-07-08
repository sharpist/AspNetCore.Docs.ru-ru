---
title: Миграция с аутентификации членства ASP.NET на ASP.NET Core 2,0Identity
author: isaac2004
description: Узнайте, как перенести существующие приложения ASP.NET с помощью проверки подлинности членства в ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: afad542a18a357a77f4542511a3d2c3108dbfb31
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059777"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="7a415-103">Миграция с аутентификации членства ASP.NET на ASP.NET Core 2,0Identity</span><span class="sxs-lookup"><span data-stu-id="7a415-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="7a415-104">Автор [Айзек Левин](https://isaaclevin.com) (Isaac Levin)</span><span class="sxs-lookup"><span data-stu-id="7a415-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="7a415-105">В этой статье показано, как перенести схему базы данных для приложений ASP.NET, используя проверку подлинности членства, для ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="7a415-106">Этот документ содержит шаги, необходимые для переноса схемы базы данных для приложений, основанных на членстве ASP.NET, в схему базы данных, используемую для ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="7a415-107">Дополнительные сведения о миграции из ASP.NET с проверкой подлинности на основе членства в ASP.NET Identity см. в статье [Перенос существующего приложения Identity из членства SQL в ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="7a415-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="7a415-108">Дополнительные сведения о ASP.NET Core Identity см. в статье [Введение в Identity на ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7a415-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="7a415-109">Проверка схемы членства</span><span class="sxs-lookup"><span data-stu-id="7a415-109">Review of Membership schema</span></span>

<span data-ttu-id="7a415-110">До ASP.NET 2,0 разработчики создавали задачу создания всего процесса проверки подлинности и авторизации для своих приложений.</span><span class="sxs-lookup"><span data-stu-id="7a415-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="7a415-111">Благодаря ASP.NET 2,0 было введено членство, предоставляя стандартное решение для обработки безопасности в приложениях ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7a415-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="7a415-112">Теперь разработчикам удалось выполнить загрузку схемы в SQL Server базу данных с помощью команды [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="7a415-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="7a415-113">После выполнения этой команды в базе данных были созданы следующие таблицы.</span><span class="sxs-lookup"><span data-stu-id="7a415-113">After running this command, the following tables were created in the database.</span></span>

  ![Таблицы членства](identity/_static/membership-tables.png)

<span data-ttu-id="7a415-115">Чтобы перенести существующие приложения на ASP.NET Core 2,0 Identity , данные в этих таблицах необходимо перенести в таблицы, используемые новой Identity схемой.</span><span class="sxs-lookup"><span data-stu-id="7a415-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="7a415-116">IdentityСхема ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="7a415-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="7a415-117">ASP.NET Core 2,0 соответствует [Identity](/aspnet/identity/index) принципу, представленному в ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="7a415-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="7a415-118">Хотя принцип является общим, реализация между платформами отличается даже между версиями ASP.NET Core (см. статью [Миграция проверки подлинности и Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="7a415-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="7a415-119">Самый быстрый способ просмотреть схему для ASP.NET Core 2,0 Identity — создать новое приложение ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="7a415-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="7a415-120">Выполните следующие действия в Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="7a415-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="7a415-121">Выберите **Файл** > **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="7a415-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7a415-122">Создайте новый проект **веб-приложения ASP.NET Core** с именем *кореидентитисампле*.</span><span class="sxs-lookup"><span data-stu-id="7a415-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="7a415-123">Выберите **ASP.NET Core 2,0** в раскрывающемся списке и выберите **веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="7a415-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="7a415-124">Этот шаблон создает приложение " [ Razor страницы](xref:razor-pages/index) ".</span><span class="sxs-lookup"><span data-stu-id="7a415-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7a415-125">Перед нажатием кнопки **ОК**щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="7a415-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="7a415-126">Выберите **учетные записи отдельных пользователей** для Identity шаблонов.</span><span class="sxs-lookup"><span data-stu-id="7a415-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="7a415-127">Наконец, нажмите кнопку **ОК**, а затем **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7a415-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="7a415-128">Visual Studio создает проект с помощью шаблона ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="7a415-129">Выберите **инструменты**  >  **Диспетчер пакетов NuGet**  >  **консоль диспетчера** пакетов, чтобы открыть окно **консоли диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="7a415-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="7a415-130">Перейдите к корневому каталогу проекта в PMC и выполните команду [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="7a415-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="7a415-131">ASP.NET Core 2,0 Identity использует EF Core для взаимодействия с базой данных, в которой хранятся данные проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="7a415-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="7a415-132">Чтобы вновь созданное приложение работало, для хранения этих данных необходимо иметь базу данных.</span><span class="sxs-lookup"><span data-stu-id="7a415-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="7a415-133">После создания нового приложения самым быстрым способом проверки схемы в среде базы данных является создание базы данных с помощью [EF Core миграций](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="7a415-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="7a415-134">Этот процесс создает базу данных, как локально, так и в других местах, которая имитирует эту схему.</span><span class="sxs-lookup"><span data-stu-id="7a415-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="7a415-135">Дополнительные сведения см. в приведенной выше документации.</span><span class="sxs-lookup"><span data-stu-id="7a415-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="7a415-136">EF Core команды используют строку подключения для базы данных, указанной в *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="7a415-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="7a415-137">Следующая строка подключения предназначена для базы данных на *узле localhost* с именем *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="7a415-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="7a415-138">В этом параметре EF Core настроен для использования `DefaultConnection` строки подключения.</span><span class="sxs-lookup"><span data-stu-id="7a415-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="7a415-139">Выберите **Просмотр**  >  **Обозреватель объектов SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="7a415-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="7a415-140">Разверните узел, соответствующий имени базы данных, указанной в `ConnectionStrings:DefaultConnection` свойстве *appsettings.jsв*.</span><span class="sxs-lookup"><span data-stu-id="7a415-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="7a415-141">`Update-Database`Команда создала базу данных, указанную в схеме, и все данные, необходимые для инициализации приложения.</span><span class="sxs-lookup"><span data-stu-id="7a415-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="7a415-142">На следующем рисунке показана структура таблицы, созданная на предыдущих шагах.</span><span class="sxs-lookup"><span data-stu-id="7a415-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    <span data-ttu-id="7a415-143">![IdentityТаблице](identity/_static/identity-tables.png)</span><span class="sxs-lookup"><span data-stu-id="7a415-143">![Identity Tables](identity/_static/identity-tables.png)</span></span>

## <a name="migrate-the-schema"></a><span data-ttu-id="7a415-144">Перенос схемы</span><span class="sxs-lookup"><span data-stu-id="7a415-144">Migrate the schema</span></span>

<span data-ttu-id="7a415-145">Существуют небольшие различия в структурах таблиц и полях для членства и ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="7a415-146">Шаблон существенно изменился для проверки подлинности и авторизации с помощью ASP.NET и ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="7a415-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="7a415-147">Ключевые объекты, которые все еще используются с, Identity — это *Пользователи* и *роли*.</span><span class="sxs-lookup"><span data-stu-id="7a415-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="7a415-148">Ниже приведены таблицы сопоставления для *пользователей*, *ролей*и *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="7a415-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="7a415-149">Пользователи</span><span class="sxs-lookup"><span data-stu-id="7a415-149">Users</span></span>

|Identity<br><span data-ttu-id="7a415-150">( `dbo.AspNetUsers` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="7a415-151">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-151">Type</span></span>     |<span data-ttu-id="7a415-152">Членство</span><span class="sxs-lookup"><span data-stu-id="7a415-152">Membership</span></span><br><span data-ttu-id="7a415-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="7a415-154">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="7a415-155">Не все сопоставления полей похожи на отношения "один к одному" от членства в ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="7a415-156">Приведенная выше таблица принимает пользовательскую схему членства по умолчанию и сопоставляет ее со Identity схемой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a415-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="7a415-157">Любые другие настраиваемые поля, которые использовались для членства, должны быть сопоставлены вручную.</span><span class="sxs-lookup"><span data-stu-id="7a415-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="7a415-158">В этом сопоставлении нет карты для паролей, так как критерии пароля и Salt пароля не переносятся между ними.</span><span class="sxs-lookup"><span data-stu-id="7a415-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="7a415-159">**Рекомендуется оставить пароль как NULL и попросить пользователей сбросить свои пароли.**</span><span class="sxs-lookup"><span data-stu-id="7a415-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="7a415-160">Identity `LockoutEnd` Если пользователь заблокирован, в ASP.NET Core должен быть указана какая-либо дата в будущем. Это показано в скрипте миграции.</span><span class="sxs-lookup"><span data-stu-id="7a415-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="7a415-161">Роли</span><span class="sxs-lookup"><span data-stu-id="7a415-161">Roles</span></span>

|Identity<br><span data-ttu-id="7a415-162">( `dbo.AspNetRoles` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="7a415-163">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-163">Type</span></span>|<span data-ttu-id="7a415-164">Членство</span><span class="sxs-lookup"><span data-stu-id="7a415-164">Membership</span></span><br><span data-ttu-id="7a415-165">( `dbo.aspnet_Roles` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="7a415-166">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="7a415-167">Роли пользователя</span><span class="sxs-lookup"><span data-stu-id="7a415-167">User Roles</span></span>

|Identity<br><span data-ttu-id="7a415-168">( `dbo.AspNetUserRoles` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="7a415-169">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-169">Type</span></span>|<span data-ttu-id="7a415-170">Членство</span><span class="sxs-lookup"><span data-stu-id="7a415-170">Membership</span></span><br><span data-ttu-id="7a415-171">( `dbo.aspnet_UsersInRoles` ) столбец</span><span class="sxs-lookup"><span data-stu-id="7a415-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="7a415-172">Тип</span><span class="sxs-lookup"><span data-stu-id="7a415-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="7a415-173">При создании скрипта миграции для *пользователей* и *ролей*следует ссылаться на предыдущие таблицы сопоставления.</span><span class="sxs-lookup"><span data-stu-id="7a415-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="7a415-174">В следующем примере предполагается, что на сервере базы данных имеются две базы данных.</span><span class="sxs-lookup"><span data-stu-id="7a415-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="7a415-175">Одна база данных содержит существующую схему и данные членства ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7a415-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="7a415-176">Другая база данных *кореидентитисампле* была создана с помощью описанных выше действий.</span><span class="sxs-lookup"><span data-stu-id="7a415-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="7a415-177">Для получения дополнительных сведений комментарии включаются в текст.</span><span class="sxs-lookup"><span data-stu-id="7a415-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="7a415-178">После завершения предыдущего сценария Identity созданное ранее приложение ASP.NET Core заполняется пользователями членства.</span><span class="sxs-lookup"><span data-stu-id="7a415-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="7a415-179">Пользователям необходимо изменить пароли перед входом в систему.</span><span class="sxs-lookup"><span data-stu-id="7a415-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="7a415-180">Если в системе членства пользователи с именами пользователей, которые не совпали с адресом электронной почты, для этого потребуется внести изменения в приложение, созданное ранее.</span><span class="sxs-lookup"><span data-stu-id="7a415-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="7a415-181">Шаблон по умолчанию принимает `UserName` и `Email` должен совпадать.</span><span class="sxs-lookup"><span data-stu-id="7a415-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="7a415-182">Для ситуаций, в которых они отличаются, процесс входа в систему необходимо изменить, чтобы использовать `UserName` вместо `Email` .</span><span class="sxs-lookup"><span data-stu-id="7a415-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="7a415-183">На `PageModel` странице входа, расположенной по адресу *пажес\аккаунт\логин.кштмл.КС*, удалите `[EmailAddress]` атрибут из свойства *Email* .</span><span class="sxs-lookup"><span data-stu-id="7a415-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="7a415-184">Переименуйте его в *username*.</span><span class="sxs-lookup"><span data-stu-id="7a415-184">Rename it to *UserName*.</span></span> <span data-ttu-id="7a415-185">Это требует изменения в любом месте `EmailAddress` , которое упоминается в *представлении* и *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="7a415-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="7a415-186">Результат имеет следующий вид.</span><span class="sxs-lookup"><span data-stu-id="7a415-186">The result looks like the following:</span></span>

 ![Фиксированное имя входа](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="7a415-188">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="7a415-188">Next steps</span></span>

<span data-ttu-id="7a415-189">В этом руководстве вы узнали, как перенести пользователей из членства SQL в ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="7a415-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="7a415-190">Дополнительные сведения о ASP.NET Core см Identity . [в статье Введение Identity в ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7a415-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
