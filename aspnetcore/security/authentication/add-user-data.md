---
title: Добавление, скачивание и удаление данных пользователя Identity в проекте ASP.NET Core
author: rick-anderson
description: Узнайте, как добавлять пользовательские данные Identity в проект ASP.NET Core. Удаление данных на GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777336"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Добавление, скачивание и удаление настраиваемых данных пользователя для идентификации в проекте ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этой статье показано, как сделать следующее:

* Добавление настраиваемых пользовательских данных в веб-приложение ASP.NET Core.
* Пометьте пользовательскую модель данных <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> атрибутом, чтобы она автоматически была доступна для скачивания и удаления. Обеспечение возможности загрузки и удаления данных помогает удовлетворить требования [GDPR](xref:security/gdpr) .

Пример проекта создается на основе веб-приложения Razor Pages, но эти инструкции похожи на ASP.NET Core веб-приложения MVC.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Создание веб-приложения Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**. Присвойте проекту имя **APP1** , если вы хотите, чтобы оно соответствовало пространству имен примера кода для [скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Выберите **ASP.NET Core веб-приложение** > **ОК**
* Выберите **ASP.NET Core 3,0** в раскрывающемся списке.
* Выберите **веб-приложение** > **ОК** .
* Постройте и запустите проект.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**. Присвойте проекту имя **APP1** , если вы хотите, чтобы оно соответствовало пространству имен примера кода для [скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Выберите **ASP.NET Core веб-приложение** > **ОК**
* Выберите **ASP.NET Core 2,2** в раскрывающемся списке.
* Выберите **веб-приложение** > **ОК** .
* Постройте и запустите проект.

::: moniker-end


# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Запуск шаблона удостоверений

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В **Обозреватель решений**щелкните правой кнопкой мыши проект > **Добавить** > **Новый**шаблонный элемент.
* В левой области диалогового окна **Добавление шаблона** выберите **удостоверение** > **Добавить**.
* В диалоговом окне **Добавление удостоверения** выполните следующие действия.
  * Выберите существующий файл макета *~/пажес/шаред/_layout. cshtml*
  * Выберите следующие файлы для переопределения:
    * **Учетная запись или регистр**
    * **Учетная запись/управление/индекс**
  * Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**. Примите тип ("имя_проекта **. Models. WebApp1Context** ", если проект называется " **APP1**").
  * Нажмите **+** кнопку, чтобы создать новый **класс пользователя**. Примите тип (**WebApp1User** , если проект называется "имя_проекта **") >** **добавить**.
* Нажмите кнопку **Добавить**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Добавьте ссылку на пакет в [Microsoft. VisualStudio. Web. стратегию. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) в файл проекта (с расширением CSPROJ). Выполните следующую команду в каталоге проекта:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Выполните следующую команду, чтобы вывести список параметров шаблона удостоверений:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

В папке проекта запустите механизм формирования идентификаторов:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Следуйте инструкциям в разделе [миграция, усеаусентикатион и макет](xref:security/authentication/scaffold-identity#efm) , чтобы выполнить следующие действия.

* Создайте миграцию и обновите базу данных.
* Добавьте `UseAuthentication` в `Startup.Configure`.
* Добавьте `<partial name="_LoginPartial" />` в файл макета.
* Проверьте работу приложения:
  * Регистрация пользователя
  * Выберите новое имя пользователя (рядом с ссылкой для **выхода** ). Может потребоваться развернуть окно или выбрать значок панели навигации, чтобы отобразить имя пользователя и другие ссылки.
  * Перейдите на вкладку **личные данные** .
  * Нажмите кнопку **скачать** и рассмотрели файл *персоналдата. JSON* .
  * Протестируйте кнопку **Удалить** , которая удаляет пользователя, выполнившего вход в систему.

## <a name="add-custom-user-data-to-the-identity-db"></a>Добавление настраиваемых данных пользователя в базу данных удостоверений

Обновите `IdentityUser` производный класс с помощью пользовательских свойств. Если вы назвали имя проекта Project, файл будет называться *Areas/Identity/Data/WebApp1User. CS*. Обновите файл, используя следующий код:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Свойства с атрибутом [персоналдата](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) :

* Удаляется при вызове `UserManager.Delete`страницы Razor *Areas/Identity/Pages/Account/Manage/делетеперсоналдата. cshtml* .
* Включается в Скачанные данные на странице Razor *Areas/Identity/Pages/Account/Manage/довнлоадперсоналдата. cshtml* .

### <a name="update-the-accountmanageindexcshtml-page"></a>Обновление страницы "учетная запись/управление/индекс. cshtml"

Обновите `InputModel` в *области, идентификатор, страницы/учетная запись/управление/индекс. cshtml. CS* со следующим выделенным кодом:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Обновите *области, идентификаторы, страницы, учетные записи, а также управление/index. cshtml* с помощью следующей выделенной разметки:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Обновите *области, идентификаторы, страницы, учетные записи, а также управление/index. cshtml* с помощью следующей выделенной разметки:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Обновление страницы "учетная запись/регистрация. cshtml"

Обновите `InputModel` в *области, идентификатор, страницы, учетная запись/Register. cshtml. CS* со следующим выделенным кодом:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Обновите *области, идентификаторы, страницы, учетную запись или Register. cshtml* со следующей выделенной разметкой:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Обновите *области, идентификаторы, страницы, учетную запись или Register. cshtml* со следующей выделенной разметкой:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Выполните построение проекта.

### <a name="add-a-migration-for-the-custom-user-data"></a>Добавление миграции для настраиваемых данных пользователя

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В **консоли диспетчера пакетов**Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Тестирование создания, просмотра, загрузки, удаления пользовательских данных пользователя

Проверьте работу приложения:

* Регистрация нового пользователя.
* Просмотр настраиваемых данных пользователя на `/Identity/Account/Manage` странице.
* Скачайте и просмотрите персональные данные пользователей на `/Identity/Account/Manage/PersonalData` странице.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Добавление утверждений Identity для использования иусерклаимспринЦипалфактори<ApplicationUser>

Дополнительные утверждения можно добавить в ASP.NET Core Identity с помощью `IUserClaimsPrincipalFactory<T>` интерфейса. Этот класс можно добавить в приложение в `Startup.ConfigureServices` методе. Добавьте пользовательскую реализацию класса следующим образом:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

В демонстрационном коде используется `ApplicationUser` класс. Этот класс добавляет `IsAdmin` свойство, которое используется для добавления дополнительного утверждения.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

Класс `AdditionalUserClaimsPrincipalFactory` реализует интерфейс `UserClaimsPrincipalFactory`. Новое утверждение роли добавляется в `ClaimsPrincipal`.

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Затем в приложении можно использовать дополнительное утверждение. На Razor странице `IAuthorizationService` экземпляр можно использовать для доступа к значению утверждения.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
