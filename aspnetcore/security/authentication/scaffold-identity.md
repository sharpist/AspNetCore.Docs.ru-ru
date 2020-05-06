---
title: Формирование Identity шаблонов в ASP.NET Core проектах
author: rick-anderson
description: Сведения о формировании Identity шаблонов в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768394"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Формирование Identity шаблонов в ASP.NET Core проектах

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class). Приложения, которые Identity включают в себя, могут применять механизм формирования шаблонов для выборочного добавления исходного кода Identity Razor , содержащегося в библиотеке классов (РКЛ). Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение. Например, вы можете указать шаблону создать код, используемый при регистрации. Созданный код имеет приоритет над тем же кодом в Identity РКЛ. Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).

Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов Identity для добавления пакета РКЛ. Вы можете выбрать Identity код для создания.

Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс. В этом документе объясняются шаги, необходимые для Identity завершения обновления формирования шаблонов.

Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения. Проверьте изменения после запуска Identity механизма формирования шаблонов.

Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также Identityдругих функций безопасности с. Службы или заглушки служб не создаются при формировании шаблонов Identity. Службы для включения этих функций необходимо добавить вручную. Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).

При формировании Identity шаблона с новым контекстом данных в проекте с существующими индивидуальными учетными записями:

* В `Startup.ConfigureServices`удалите вызовы:
  * `AddDbContext`
  * `AddDefaultIdentity`

Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Предшествующий код записывает в комментарий код, который дублируется в *областиIdentity//IdentityHostingStartup.CS*

Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.

## <a name="scaffold-identity-into-an-empty-project"></a>Удостоверение шаблона в пустой проект

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Удостоверение шаблона в Razor проекте без существующей авторизации

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityнастраивается в *областиIdentity//IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Миграция, Усеаусентикатион и макет

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Включить проверку подлинности

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Изменения макета

Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Удостоверение шаблона в Razor проекте с авторизацией

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Некоторые Identity параметры настраиваются в *области/Identity/IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Удостоверение шаблона в проекте MVC без существующей авторизации

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*

Identityнастраивается в *областиIdentity//IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Удостоверение шаблона в проекте MVC с авторизацией

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Создание полного источника идентификатора пользовательского интерфейса

Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, Identity запустите шаблон и выберите **переопределить все файлы**.

В следующем выделенном коде показаны изменения для замены пользовательского интерфейса Identity по умолчанию Identity в веб-приложении ASP.NET Core 2,1. Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Значение по Identity умолчанию заменяется в следующем коде:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Зарегистрируйте `IEmailSender` реализацию, например:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Отключить страницу регистрации

Чтобы отключить регистрацию пользователей, выполните следующие действия.

* Шаблон Identity. Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион. Например:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Обновите *областиIdentityили/Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Обновите *областиIdentity//Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Закомментируйте или удалите регистрационную ссылку из *областейIdentity//Пажес/аккаунт/логин.кштмл*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Обновите страницу *областиIdentityили/Пажес/аккаунт/регистерконфирматион* .

  * Удалите код и ссылки из файла CSHTML.
  * Удалите код подтверждения из `PageModel`:

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Использование другого приложения для добавления пользователей

Предоставьте механизм для добавления пользователей за пределами веб-приложения. Ниже перечислены параметры для добавления пользователей.

* Выделенное административное веб-приложение.
* Консольное приложение.

В следующем примере кода один из подходов к добавлению пользователей:

* Список пользователей считывается в память.
* Для каждого пользователя создается надежный уникальный пароль.
* Пользователь будет добавлен в Identity базу данных.
* Пользователь получает уведомления и сообщил об изменении пароля.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

В следующем коде показано, как добавить пользователя:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Подобный подход можно выполнить для рабочих сценариев.

## <a name="prevent-publish-of-static-identity-assets"></a>Запретить публикацию статических Identity ресурсов

Чтобы предотвратить публикацию Identity статических ресурсов в веб-корне <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>, см. раздел.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 и более поздних версий предоставляет [ Identity ASP.NET Core](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class). Приложения, которые Identity включают в себя, могут применять механизм формирования шаблонов для выборочного добавления исходного кода Identity Razor , содержащегося в библиотеке классов (РКЛ). Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение. Например, вы можете указать шаблону создать код, используемый при регистрации. Созданный код имеет приоритет над тем же кодом в Identity РКЛ. Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).

Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов Identity для добавления пакета РКЛ. Вы можете выбрать Identity код для создания.

Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс. В этом документе объясняются шаги, необходимые для Identity завершения обновления формирования шаблонов.

При запуске Identity шаблона в каталоге проекта создается файл *скаффолдингреадме. txt* . Файл *скаффолдингреадме. txt* содержит общие инструкции о том, что необходимо для завершения обновления Identity формирования шаблонов. Этот документ содержит более полные инструкции, чем файл *скаффолдингреадме. txt* .

Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения. Проверьте изменения после запуска Identity механизма формирования шаблонов.

> [!NOTE]
> Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также Identityдругих функций безопасности с. Службы или заглушки служб не создаются при формировании шаблонов Identity. Службы для включения этих функций необходимо добавить вручную. Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Удостоверение шаблона в пустой проект

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Добавьте следующие выделенные вызовы в `Startup` класс:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Удостоверение шаблона в Razor проекте без существующей авторизации

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityнастраивается в *областиIdentity//IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Миграция, Усеаусентикатион и макет

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Включить проверку подлинности

В `Configure` `Startup` методе класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Изменения макета

Необязательно: Добавьте в файл макета`_LoginPartial`имя для входа partial ():

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Удостоверение шаблона в Razor проекте с авторизацией

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Некоторые Identity параметры настраиваются в *области/Identity/IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Удостоверение шаблона в проекте MVC без существующей авторизации

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Необязательно: Добавьте часть имени для`_LoginPartial`входа partial () в файл *views/shared/_layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*

Identityнастраивается в *областиIdentity//IdentityHostingStartup.CS*. Дополнительные сведения см. в разделе IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Удостоверение шаблона в проекте MVC с авторизацией

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Удалите папки *pages/Shared* и файлы в этой папке.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Создание полного источника идентификатора пользовательского интерфейса

Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, Identity запустите шаблон и выберите **переопределить все файлы**.

В следующем выделенном коде показаны изменения для замены пользовательского интерфейса Identity по умолчанию Identity в веб-приложении ASP.NET Core 2,1. Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Значение по Identity умолчанию заменяется в следующем коде:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Следующий код задает [логинпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [логаутпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)и [акцессдениедпас](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Зарегистрируйте `IEmailSender` реализацию, например:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Отключить страницу регистрации

Чтобы отключить регистрацию пользователей, выполните следующие действия.

* Шаблон Identity. Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион. Например:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Обновите *областиIdentityили/Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Обновите *областиIdentity//Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Закомментируйте или удалите регистрационную ссылку из *областейIdentity//Пажес/аккаунт/логин.кштмл*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Обновите страницу *областиIdentityили/Пажес/аккаунт/регистерконфирматион* .

  * Удалите код и ссылки из файла CSHTML.
  * Удалите код подтверждения из `PageModel`:

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Использование другого приложения для добавления пользователей

Предоставьте механизм для добавления пользователей за пределами веб-приложения. Ниже перечислены параметры для добавления пользователей.

* Выделенное административное веб-приложение.
* Консольное приложение.

В следующем примере кода один из подходов к добавлению пользователей:

* Список пользователей считывается в память.
* Для каждого пользователя создается надежный уникальный пароль.
* Пользователь будет добавлен в Identity базу данных.
* Пользователь получает уведомления и сообщил об изменении пароля.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

В следующем коде показано, как добавить пользователя:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Подобный подход можно выполнить для рабочих сценариев.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end