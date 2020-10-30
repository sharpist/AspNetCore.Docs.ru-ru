---
title: Миграция с аутентификации членства ASP.NET на ASP.NET Core 2,0 Identity
author: isaac2004
description: Узнайте, как перенести существующие приложения ASP.NET с помощью проверки подлинности членства в ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051360"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>Миграция с аутентификации членства ASP.NET на ASP.NET Core 2,0 Identity

Автор [Айзек Левин](https://isaaclevin.com) (Isaac Levin)

В этой статье показано, как перенести схему базы данных для приложений ASP.NET, используя проверку подлинности членства, для ASP.NET Core 2,0 Identity .

> [!NOTE]
> Этот документ содержит шаги, необходимые для переноса схемы базы данных для приложений, основанных на членстве ASP.NET, в схему базы данных, используемую для ASP.NET Core Identity . Дополнительные сведения о миграции из ASP.NET с проверкой подлинности на основе членства в ASP.NET Identity см. в статье [Перенос существующего приложения Identity из членства SQL в ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Дополнительные сведения о см ASP.NET Core Identity . в разделе [Введение в Identity на ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Проверка схемы членства

До ASP.NET 2,0 разработчики создавали задачу создания всего процесса проверки подлинности и авторизации для своих приложений. Благодаря ASP.NET 2,0 было введено членство, предоставляя стандартное решение для обработки безопасности в приложениях ASP.NET. Теперь разработчикам удалось выполнить загрузку схемы в SQL Server базу данных с помощью <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> команды. После выполнения этой команды в базе данных были созданы следующие таблицы.

  ![Таблицы членства](identity/_static/membership-tables.png)

Чтобы перенести существующие приложения на ASP.NET Core 2,0 Identity , данные в этих таблицах необходимо перенести в таблицы, используемые новой Identity схемой.

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity Схема 2,0

ASP.NET Core 2,0 соответствует [Identity](/aspnet/identity/index) принципу, представленному в ASP.NET 4,5. Хотя принцип является общим, реализация между платформами отличается даже между версиями ASP.NET Core (см. статью [Миграция проверки подлинности и Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

Самый быстрый способ просмотреть схему для ASP.NET Core 2,0 Identity — создать новое приложение ASP.NET Core 2,0. Выполните следующие действия в Visual Studio 2017:

1. Выберите **File** > **New** > **Project** ( Файл > Создать > Проект).
1. Создайте новый проект **веб-приложения ASP.NET Core** с *именем Identity Core* .
1. Выберите **ASP.NET Core 2,0** в раскрывающемся списке и выберите **веб-приложение** . Этот шаблон создает приложение " [ Razor страницы](xref:razor-pages/index) ". Перед нажатием кнопки **ОК** щелкните **изменить проверку подлинности** .
1. Выберите **учетные записи отдельных пользователей** для Identity шаблонов. Наконец, нажмите кнопку **ОК** , а затем **ОК** . Visual Studio создает проект с помощью ASP.NET Core Identity шаблона.
1. Выберите **инструменты**  >  **Диспетчер пакетов NuGet**  >  **консоль диспетчера** пакетов, чтобы открыть окно **консоли диспетчера пакетов** (PMC).
1. Перейдите к корневому каталогу проекта в PMC и выполните команду [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity использует EF Core для взаимодействия с базой данных, в которой хранятся данные проверки подлинности. Чтобы вновь созданное приложение работало, для хранения этих данных необходимо иметь базу данных. После создания нового приложения самым быстрым способом проверки схемы в среде базы данных является создание базы данных с помощью [EF Core миграций](/ef/core/managing-schemas/migrations/). Этот процесс создает базу данных, как локально, так и в других местах, которая имитирует эту схему. Дополнительные сведения см. в приведенной выше документации.

    EF Core команды используют строку подключения для базы данных, указанной в параметре *appsettings.json* . Следующая строка подключения предназначена для базы данных на *узле localhost* с именем *ASP-NET-Core-Identity* . В этом параметре EF Core настроен для использования `DefaultConnection` строки подключения.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Выберите **Просмотр**  >  **Обозреватель объектов SQL Server** . Разверните узел, соответствующий имени базы данных, указанной в `ConnectionStrings:DefaultConnection` свойстве *appsettings.json* .

    `Update-Database`Команда создала базу данных, указанную в схеме, и все данные, необходимые для инициализации приложения. На следующем рисунке показана структура таблицы, созданная на предыдущих шагах.

    ![::: No-Loc (идентификатор)::: таблицы](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Перенос схемы

Существуют небольшие различия в структурах таблиц и полях для членства и ASP.NET Core Identity . Шаблон существенно изменился для проверки подлинности и авторизации с помощью ASP.NET и ASP.NET Core приложений. Ключевые объекты, которые все еще используются с, Identity — это *Пользователи* и *роли* . Ниже приведены таблицы сопоставления для *пользователей* , *ролей* и *UserRoles* .

### <a name="users"></a>Пользователи

|Identity<br>( `dbo.AspNetUsers` ) столбец  |Тип     |Членство<br>( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) столбец|Тип      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Не все сопоставления полей похожи на отношения "один к одному" от членства в ASP.NET Core Identity . Приведенная выше таблица принимает пользовательскую схему членства по умолчанию и сопоставляет ее со ASP.NET Core Identity схемой. Любые другие настраиваемые поля, которые использовались для членства, должны быть сопоставлены вручную. В этом сопоставлении нет карты для паролей, так как критерии пароля и Salt пароля не переносятся между ними. **Рекомендуется оставить пароль как NULL и попросить пользователей сбросить свои пароли.** В ASP.NET Core Identity необходимо `LockoutEnd` задать дату в будущем, если пользователь заблокирован. Это показано в скрипте миграции.

### <a name="roles"></a>Роли

|Identity<br>( `dbo.AspNetRoles` ) столбец|Тип|Членство<br>( `dbo.aspnet_Roles` ) столбец|Тип|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>Роли пользователя

|Identity<br>( `dbo.AspNetUserRoles` ) столбец|Тип|Членство<br>( `dbo.aspnet_UsersInRoles` ) столбец|Тип|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

При создании скрипта миграции для *пользователей* и *ролей* следует ссылаться на предыдущие таблицы сопоставления. В следующем примере предполагается, что на сервере базы данных имеются две базы данных. Одна база данных содержит существующую схему и данные членства ASP.NET. Другой *основной Identity образец* базы данных был создан с помощью описанных выше действий. Для получения дополнительных сведений комментарии включаются в текст.

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

После завершения предыдущего скрипта ASP.NET Core Identity созданное ранее приложение заполняется пользователями членства. Пользователям необходимо изменить пароли перед входом в систему.

> [!NOTE]
> Если в системе членства пользователи с именами пользователей, которые не совпали с адресом электронной почты, для этого потребуется внести изменения в приложение, созданное ранее. Шаблон по умолчанию принимает `UserName` и `Email` должен совпадать. Для ситуаций, в которых они отличаются, процесс входа в систему необходимо изменить, чтобы использовать `UserName` вместо `Email` .

На `PageModel` странице входа, расположенной по адресу *пажес\аккаунт\логин.кштмл.КС* , удалите `[EmailAddress]` атрибут из свойства *Email* . Переименуйте его в *username* . Это требует изменения в любом месте `EmailAddress` , которое упоминается в *представлении* и *PageModel* . Результат имеет следующий вид.

 ![Фиксированное имя входа](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как перенести пользователей из членства SQL в ASP.NET Core 2,0 Identity . Дополнительные сведения о см ASP.NET Core Identity . [в разделе Введение Identity в ](xref:security/authentication/identity).
