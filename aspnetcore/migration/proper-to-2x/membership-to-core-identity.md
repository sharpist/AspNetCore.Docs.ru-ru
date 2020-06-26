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
ms.openlocfilehash: f039772f4276d0e8bcec2629350eba2ec0e7418c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399690"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="6891a-103">Миграция с аутентификации членства ASP.NET на ASP.NET Core 2,0Identity</span><span class="sxs-lookup"><span data-stu-id="6891a-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="6891a-104">Автор [Айзек Левин](https://isaaclevin.com) (Isaac Levin)</span><span class="sxs-lookup"><span data-stu-id="6891a-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="6891a-105">В этой статье показано, как перенести схему базы данных для приложений ASP.NET, используя проверку подлинности членства, для ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="6891a-106">Этот документ содержит шаги, необходимые для переноса схемы базы данных для приложений, основанных на членстве ASP.NET, в схему базы данных, используемую для ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="6891a-107">Дополнительные сведения о миграции из ASP.NET с проверкой подлинности на основе членства в ASP.NET Identity см. в статье [Перенос существующего приложения Identity из членства SQL в ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="6891a-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="6891a-108">Дополнительные сведения о ASP.NET Core Identity см. в статье [Введение в Identity на ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="6891a-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="6891a-109">Проверка схемы членства</span><span class="sxs-lookup"><span data-stu-id="6891a-109">Review of Membership schema</span></span>

<span data-ttu-id="6891a-110">До ASP.NET 2,0 разработчики создавали задачу создания всего процесса проверки подлинности и авторизации для своих приложений.</span><span class="sxs-lookup"><span data-stu-id="6891a-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="6891a-111">Благодаря ASP.NET 2,0 было введено членство, предоставляя стандартное решение для обработки безопасности в приложениях ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6891a-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="6891a-112">Теперь разработчикам удалось выполнить загрузку схемы в SQL Server базу данных с помощью команды [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="6891a-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="6891a-113">После выполнения этой команды в базе данных были созданы следующие таблицы.</span><span class="sxs-lookup"><span data-stu-id="6891a-113">After running this command, the following tables were created in the database.</span></span>

  ![Таблицы членства](identity/_static/membership-tables.png)

<span data-ttu-id="6891a-115">Чтобы перенести существующие приложения на ASP.NET Core 2,0 Identity , данные в этих таблицах необходимо перенести в таблицы, используемые новой Identity схемой.</span><span class="sxs-lookup"><span data-stu-id="6891a-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="6891a-116">IdentityСхема ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="6891a-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="6891a-117">ASP.NET Core 2,0 соответствует [Identity](/aspnet/identity/index) принципу, представленному в ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="6891a-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="6891a-118">Хотя принцип является общим, реализация между платформами отличается даже между версиями ASP.NET Core (см. статью [Миграция проверки подлинности и Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="6891a-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="6891a-119">Самый быстрый способ просмотреть схему для ASP.NET Core 2,0 Identity — создать новое приложение ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="6891a-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="6891a-120">Выполните следующие действия в Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="6891a-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="6891a-121">Выберите **File** > **New** > **Project** ( Файл > Создать > Проект).</span><span class="sxs-lookup"><span data-stu-id="6891a-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6891a-122">Создайте новый проект **веб-приложения ASP.NET Core** с именем *кореидентитисампле*.</span><span class="sxs-lookup"><span data-stu-id="6891a-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="6891a-123">Выберите **ASP.NET Core 2,0** в раскрывающемся списке и выберите **веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="6891a-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="6891a-124">Этот шаблон создает приложение " [ Razor страницы](xref:razor-pages/index) ".</span><span class="sxs-lookup"><span data-stu-id="6891a-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="6891a-125">Перед нажатием кнопки **ОК**щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="6891a-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="6891a-126">Выберите **учетные записи отдельных пользователей** для Identity шаблонов.</span><span class="sxs-lookup"><span data-stu-id="6891a-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="6891a-127">Наконец, нажмите кнопку **ОК**, а затем **ОК**.</span><span class="sxs-lookup"><span data-stu-id="6891a-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="6891a-128">Visual Studio создает проект с помощью шаблона ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="6891a-129">Выберите **инструменты**  >  **Диспетчер пакетов NuGet**  >  **консоль диспетчера** пакетов, чтобы открыть окно **консоли диспетчера пакетов** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6891a-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="6891a-130">Перейдите к корневому каталогу проекта в PMC и выполните команду [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="6891a-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="6891a-131">ASP.NET Core 2,0 Identity использует EF Core для взаимодействия с базой данных, в которой хранятся данные проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6891a-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="6891a-132">Чтобы вновь созданное приложение работало, для хранения этих данных необходимо иметь базу данных.</span><span class="sxs-lookup"><span data-stu-id="6891a-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="6891a-133">После создания нового приложения самым быстрым способом проверки схемы в среде базы данных является создание базы данных с помощью [EF Core миграций](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="6891a-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="6891a-134">Этот процесс создает базу данных, как локально, так и в других местах, которая имитирует эту схему.</span><span class="sxs-lookup"><span data-stu-id="6891a-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="6891a-135">Дополнительные сведения см. в приведенной выше документации.</span><span class="sxs-lookup"><span data-stu-id="6891a-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="6891a-136">EF Core команды используют строку подключения для базы данных, указанной в *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="6891a-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="6891a-137">Следующая строка подключения предназначена для базы данных на *узле localhost* с именем *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="6891a-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="6891a-138">В этом параметре EF Core настроен для использования `DefaultConnection` строки подключения.</span><span class="sxs-lookup"><span data-stu-id="6891a-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="6891a-139">Выберите **Просмотр**  >  **Обозреватель объектов SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="6891a-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="6891a-140">Разверните узел, соответствующий имени базы данных, указанной в `ConnectionStrings:DefaultConnection` свойстве *appsettings.jsв*.</span><span class="sxs-lookup"><span data-stu-id="6891a-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="6891a-141">`Update-Database`Команда создала базу данных, указанную в схеме, и все данные, необходимые для инициализации приложения.</span><span class="sxs-lookup"><span data-stu-id="6891a-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="6891a-142">На следующем рисунке показана структура таблицы, созданная на предыдущих шагах.</span><span class="sxs-lookup"><span data-stu-id="6891a-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    <span data-ttu-id="6891a-143">![IdentityТаблице](identity/_static/identity-tables.png)</span><span class="sxs-lookup"><span data-stu-id="6891a-143">![Identity Tables](identity/_static/identity-tables.png)</span></span>

## <a name="migrate-the-schema"></a><span data-ttu-id="6891a-144">Перенос схемы</span><span class="sxs-lookup"><span data-stu-id="6891a-144">Migrate the schema</span></span>

<span data-ttu-id="6891a-145">Существуют небольшие различия в структурах таблиц и полях для членства и ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="6891a-146">Шаблон существенно изменился для проверки подлинности и авторизации с помощью ASP.NET и ASP.NET Core приложений.</span><span class="sxs-lookup"><span data-stu-id="6891a-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="6891a-147">Ключевые объекты, которые все еще используются с, Identity — это *Пользователи* и *роли*.</span><span class="sxs-lookup"><span data-stu-id="6891a-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="6891a-148">Ниже приведены таблицы сопоставления для *пользователей*, *ролей*и *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="6891a-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="6891a-149">Пользователи</span><span class="sxs-lookup"><span data-stu-id="6891a-149">Users</span></span>

|<span data-ttu-id="6891a-150">*Identity<br>dbo. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="6891a-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="6891a-151">*Членство <br> (dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="6891a-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="6891a-152">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-152">**Field Name**</span></span>                 |<span data-ttu-id="6891a-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-153">**Type**</span></span>|<span data-ttu-id="6891a-154">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-154">**Field Name**</span></span>                                    |<span data-ttu-id="6891a-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="6891a-156">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="6891a-157">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="6891a-158">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="6891a-159">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="6891a-160">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="6891a-161">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="6891a-162">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="6891a-163">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="6891a-164">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="6891a-165">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="6891a-166">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="6891a-167">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="6891a-168">bit</span><span class="sxs-lookup"><span data-stu-id="6891a-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="6891a-169">bit</span><span class="sxs-lookup"><span data-stu-id="6891a-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="6891a-170">Не все сопоставления полей похожи на отношения "один к одному" от членства в ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="6891a-171">Приведенная выше таблица принимает пользовательскую схему членства по умолчанию и сопоставляет ее со Identity схемой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6891a-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="6891a-172">Любые другие настраиваемые поля, которые использовались для членства, должны быть сопоставлены вручную.</span><span class="sxs-lookup"><span data-stu-id="6891a-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="6891a-173">В этом сопоставлении нет карты для паролей, так как критерии пароля и Salt пароля не переносятся между ними.</span><span class="sxs-lookup"><span data-stu-id="6891a-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="6891a-174">**Рекомендуется оставить пароль как NULL и попросить пользователей сбросить свои пароли.**</span><span class="sxs-lookup"><span data-stu-id="6891a-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="6891a-175">Identity `LockoutEnd` Если пользователь заблокирован, в ASP.NET Core должен быть указана какая-либо дата в будущем. Это показано в скрипте миграции.</span><span class="sxs-lookup"><span data-stu-id="6891a-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="6891a-176">Роли</span><span class="sxs-lookup"><span data-stu-id="6891a-176">Roles</span></span>

|<span data-ttu-id="6891a-177">*Identity<br>dbo. Аспнетролес)*</span><span class="sxs-lookup"><span data-stu-id="6891a-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="6891a-178">*Членство <br> (dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="6891a-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="6891a-179">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-179">**Field Name**</span></span>                 |<span data-ttu-id="6891a-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-180">**Type**</span></span>|<span data-ttu-id="6891a-181">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-181">**Field Name**</span></span>   |<span data-ttu-id="6891a-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="6891a-183">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-183">string</span></span>  |`RoleId`         | <span data-ttu-id="6891a-184">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="6891a-185">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-185">string</span></span>  |`RoleName`       | <span data-ttu-id="6891a-186">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="6891a-187">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="6891a-188">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="6891a-189">Роли пользователя</span><span class="sxs-lookup"><span data-stu-id="6891a-189">User Roles</span></span>

|<span data-ttu-id="6891a-190">*Identity<br>dbo. Аспнетусерролес)*</span><span class="sxs-lookup"><span data-stu-id="6891a-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="6891a-191">*Членство <br> (dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="6891a-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="6891a-192">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-192">**Field Name**</span></span>           |<span data-ttu-id="6891a-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-193">**Type**</span></span>  |<span data-ttu-id="6891a-194">**Имя поля**</span><span class="sxs-lookup"><span data-stu-id="6891a-194">**Field Name**</span></span>|<span data-ttu-id="6891a-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="6891a-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="6891a-196">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-196">string</span></span>    |`RoleId`      |<span data-ttu-id="6891a-197">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="6891a-198">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-198">string</span></span>    |`UserId`      |<span data-ttu-id="6891a-199">строка</span><span class="sxs-lookup"><span data-stu-id="6891a-199">string</span></span>                     |

<span data-ttu-id="6891a-200">При создании скрипта миграции для *пользователей* и *ролей*следует ссылаться на предыдущие таблицы сопоставления.</span><span class="sxs-lookup"><span data-stu-id="6891a-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="6891a-201">В следующем примере предполагается, что на сервере базы данных имеются две базы данных.</span><span class="sxs-lookup"><span data-stu-id="6891a-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="6891a-202">Одна база данных содержит существующую схему и данные членства ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6891a-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="6891a-203">Другая база данных *кореидентитисампле* была создана с помощью описанных выше действий.</span><span class="sxs-lookup"><span data-stu-id="6891a-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="6891a-204">Для получения дополнительных сведений комментарии включаются в текст.</span><span class="sxs-lookup"><span data-stu-id="6891a-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="6891a-205">После завершения предыдущего сценария Identity созданное ранее приложение ASP.NET Core заполняется пользователями членства.</span><span class="sxs-lookup"><span data-stu-id="6891a-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="6891a-206">Пользователям необходимо изменить пароли перед входом в систему.</span><span class="sxs-lookup"><span data-stu-id="6891a-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="6891a-207">Если в системе членства пользователи с именами пользователей, которые не совпали с адресом электронной почты, для этого потребуется внести изменения в приложение, созданное ранее.</span><span class="sxs-lookup"><span data-stu-id="6891a-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="6891a-208">Шаблон по умолчанию принимает `UserName` и `Email` должен совпадать.</span><span class="sxs-lookup"><span data-stu-id="6891a-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="6891a-209">Для ситуаций, в которых они отличаются, процесс входа в систему необходимо изменить, чтобы использовать `UserName` вместо `Email` .</span><span class="sxs-lookup"><span data-stu-id="6891a-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="6891a-210">На `PageModel` странице входа, расположенной по адресу *пажес\аккаунт\логин.кштмл.КС*, удалите `[EmailAddress]` атрибут из свойства *Email* .</span><span class="sxs-lookup"><span data-stu-id="6891a-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="6891a-211">Переименуйте его в *username*.</span><span class="sxs-lookup"><span data-stu-id="6891a-211">Rename it to *UserName*.</span></span> <span data-ttu-id="6891a-212">Это требует изменения в любом месте `EmailAddress` , которое упоминается в *представлении* и *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="6891a-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="6891a-213">Результат имеет следующий вид.</span><span class="sxs-lookup"><span data-stu-id="6891a-213">The result looks like the following:</span></span>

 ![Фиксированное имя входа](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="6891a-215">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="6891a-215">Next steps</span></span>

<span data-ttu-id="6891a-216">В этом руководстве вы узнали, как перенести пользователей из членства SQL в ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="6891a-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="6891a-217">Дополнительные сведения о ASP.NET Core см Identity . [в статье Введение Identity в ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="6891a-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
