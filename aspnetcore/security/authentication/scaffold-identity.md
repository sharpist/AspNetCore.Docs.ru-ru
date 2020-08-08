---
title: Формирование шаблонов Identity в ASP.NET Core проектах
author: rick-anderson
description: Сведения о формировании шаблонов Identity в проекте ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 4404a5513d9dc989e50c904f3e7863de59991939
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022333"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>Формирование шаблонов Identity в ASP.NET Core проектах

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class). Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ). Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение. Например, вы можете указать шаблону создать код, используемый при регистрации. Созданный код имеет приоритет над тем же кодом в RCL для Identity. Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного Identity источника пользовательского интерфейса](#full).

Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ. Вы можете выбрать, какой код Identity будет создан.

Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс. В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.

Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения. Проверьте изменения после запуска Identity механизма формирования шаблонов.

Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity . Службы или заглушки служб не создаются при формировании шаблонов Identity . Службы для включения этих функций необходимо добавить вручную. Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).

При формировании шаблона Identity с новым контекстом данных в проекте с существующими индивидуальными учетными записями:

* В `Startup.ConfigureServices` удалите вызовы:
  * `AddDbContext`
  * `AddDefaultIdentity`

Например, `AddDbContext` и `AddDefaultIdentity` комментарии в следующем коде:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Предшествующий код записывает в комментарий код, который дублируется в *области или Identity / Identity HostingStartup.CS*

Как правило, приложения, созданные с помощью отдельных учетных записей, ***не*** должны создавать новый контекст данных.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Формирование шаблона Identity в пустой проект

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Формирование шаблонов Identity в Razor проекте без существующей авторизации

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

Identityнастраивается в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Миграция, Усеаусентикатион и макет

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Включить проверку подлинности

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Изменения макета

Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Формирование шаблонов Identity в Razor проекте с авторизацией

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

Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Формирование шаблонов Identity в проекте MVC без существующей авторизации

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

Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*

Identityнастраивается в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Обновите `Startup` класс с помощью кода, аналогичного следующему:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Формирование шаблонов Identity в проекте MVC с авторизацией

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Формирование шаблонов Identity в Blazor Server проекте без существующей авторизации

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityнастраивается в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Миграции

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Передача токена XSRF в приложение

Маркеры можно передавать в компоненты:

* Когда маркеры проверки подлинности подготавливаются и сохраняются в процессе проверки подлинности cookie , они могут передаваться в компоненты.
* Razorкомпоненты не могут использовать `HttpContext` напрямую, поэтому не существует способа получить [маркер подделки для защиты от запроса (XSRF)](xref:security/anti-request-forgery) для размещения Identity конечной точки выхода в `/Identity/Account/Logout` . Токен XSRF может быть передан компонентам.

Для получения дополнительной информации см. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

В файле *pages/_Host. cshtml* Установите маркер после его добавления в `InitialApplicationState` `TokenProvider` классы и:

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Обновите `App` компонент (*app. Razor*), чтобы назначить `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

`TokenProvider`Служба, показанная в разделе, используется в `LoginDisplay` компоненте в следующем разделе [Макет и изменения потока проверки подлинности](#layout-and-authentication-flow-changes) .

### <a name="enable-authentication"></a>Включить проверку подлинности

В `Startup` классе:

* Убедитесь, что Razor службы страниц добавлены в `Startup.ConfigureServices` .
* При использовании [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Зарегистрируйте службу.
* Вызовите в `UseDatabaseErrorPage` построителе приложений в `Startup.Configure` среде разработки.
* Вызовите `UseAuthentication` и `UseAuthorization` после `UseRouting` .
* Добавление конечной точки для Razor страниц.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Изменения в потоке макета и проверки подлинности

Добавьте `RedirectToLogin` компонент (*редиректтологин. Razor*) в *общую* папку приложения в корневом каталоге проекта:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Добавьте `LoginDisplay` компонент (*логиндисплай. Razor*) в *общую* папку приложения. [Служба TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) предоставляет маркер XSRF для HTML-формы, которая отправляет в Identity конечную точку выхода.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

В `MainLayout` компоненте (*Shared/маинлайаут. Razor*) добавьте `LoginDisplay` компонент в `<div>` содержимое элемента верхней строки:

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Конечные точки проверки подлинности стиля

Поскольку Blazor Server использует Razor Identity страницы страниц, стиль пользовательского интерфейса изменяется при переходе посетителя между Identity страницами и компонентами. Существует два варианта для адресации стилей инконгруаус:

#### <a name="build-no-locidentity-components"></a>IdentityКомпоненты сборки

Подход к использованию компонентов Identity вместо страниц заключается в создании Identity компонентов. Поскольку `SignInManager` и `UserManager` не поддерживаются в Razor компонентах, используйте конечные точки API в Blazor Server приложении для обработки действий с учетной записью пользователя.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Использование пользовательского макета с Blazor стилями приложения

IdentityМакет и стили страниц можно изменить для создания страниц, использующих тему по умолчанию Blazor .

> [!NOTE]
> Пример в этом разделе является просто начальной точкой для настройки. Для лучшего взаимодействия с пользователем, скорее всего, потребуется дополнительная работа.

Создайте новый `NavMenu_IdentityLayout` компонент (*Shared/NavMenu_ Identity Layout. Razor*). Для разметки и кода компонента используйте то же содержимое `NavMenu` компонента приложения (*Shared/навмену. Razor*). Вывлекайте все `NavLink` компоненты, которые не могут быть анонимно подключены, так как автоматическое перенаправление в `RedirectToLogin` компоненте не выполняется для компонентов, нуждающихся в проверке подлинности или авторизации.

В файле *pages/Shared/Layout. cshtml* внесите следующие изменения:

* Добавьте Razor директивы в начало файла, чтобы использовать вспомогательные функции тегов и компоненты приложения в *общей* папке:

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Замените `{APPLICATION ASSEMBLY}` на имя сборки приложения.

* Добавьте `<base>` тег и Blazor таблицу стилей `<link>` к `<head>` содержимому:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Измените содержимое `<body>` тега следующим образом:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Формирование шаблонов Identity в Blazor Server проекте с авторизацией

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Создание полного Identity источника пользовательского интерфейса

Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.

В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1. Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Значение по умолчанию Identity заменяется в следующем коде:

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

## <a name="password-configuration"></a>Настройка пароля

Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity . `InputModel``Password`Свойства находятся в следующих файлах:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Отключение страницы

В этом разделе показано, как отключить страницу Register, но подход можно использовать для отключения любой страницы.

Чтобы отключить регистрацию пользователей, выполните следующие действия.

* Шаблон Identity . Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион. Например:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .

  * Удалите код и ссылки из файла CSHTML.
  * Удалите код подтверждения из `PageModel` :

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

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Запретить публикацию статических Identity ресурсов

Чтобы предотвратить публикацию статических Identity ресурсов в веб-корне, см <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> . раздел.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Изменения кода проверки подлинности на ASP.NET Core 2,1 и более поздних версий](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 и более поздних версий предоставляет [ASP.NET Core Identity ](xref:security/authentication/identity) в виде [ Razor библиотеки классов](xref:razor-pages/ui-class). Приложения, которые включают в себя, Identity могут применять механизм формирования шаблонов для выборочного добавления исходного кода, содержащегося в Identity Razor библиотеке классов (РКЛ). Вы можете создать исходный код, чтобы изменить код и тем самым изменить поведение. Например, вы можете указать шаблону создать код, используемый при регистрации. Созданный код имеет приоритет над тем же кодом в RCL для Identity. Чтобы получить полный контроль над пользовательским интерфейсом и не использовать РКЛ по умолчанию, см. раздел [Создание полного идентификатора пользовательского интерфейса идентификации](#full).

Приложения, которые **не** включают проверку подлинности, могут применять механизм формирования шаблонов для добавления Identity пакета РКЛ. Вы можете выбрать, какой код Identity будет создан.

Хотя этот механизм создает большую часть необходимого кода, необходимо обновить проект, чтобы завершить процесс. В этом документе объясняются шаги, необходимые для завершения Identity обновления формирования шаблонов.

При Identity запуске шаблона создается файл *ScaffoldingReadme.txt* в каталоге проекта. Файл *ScaffoldingReadme.txt* содержит общие инструкции о том, что необходимо для завершения Identity обновления формирования шаблонов. Этот документ содержит более полные инструкции, чем файл *ScaffoldingReadme.txt* .

Рекомендуется использовать систему управления версиями, которая показывает различия в файлах и позволяет вносить изменения. Проверьте изменения после запуска Identity механизма формирования шаблонов.

> [!NOTE]
> Службы необходимы при использовании [двухфакторной проверки подлинности](xref:security/authentication/identity-enable-qrcodes), [подтверждения учетной записи и восстановления пароля](xref:security/authentication/accconfirm), а также других функций безопасности с Identity . Службы или заглушки служб не создаются при формировании шаблонов Identity . Службы для включения этих функций необходимо добавить вручную. Например, см. статью [требование подтверждения по электронной почте](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Формирование шаблона Identity в пустой проект

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Добавьте следующие выделенные вызовы в `Startup` класс:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Формирование шаблонов Identity в Razor проекте без существующей авторизации

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

Identityнастраивается в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Миграция, Усеаусентикатион и макет

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Включить проверку подлинности

В `Configure` методе `Startup` класса вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Изменения макета

Необязательно: Добавьте в файл макета имя для входа partial ( `_LoginPartial` ):

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Формирование шаблонов Identity в Razor проекте с авторизацией

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

Некоторые Identity параметры настраиваются в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Формирование шаблонов Identity в проекте MVC без существующей авторизации

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

Необязательно: Добавьте часть имени для входа partial ( `_LoginPartial` ) в файл *views/Shared/_layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Переместить файл *pages/Shared/_LoginPartial. cshtml* в *views/shared/_LoginPartial. cshtml*

Identityнастраивается в *области или Identity / Identity HostingStartup.CS*. Дополнительные сведения см. в разделе IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Вызовите [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) после `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Формирование шаблонов Identity в проекте MVC с авторизацией

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

## <a name="create-full-no-locidentity-ui-source"></a>Создание полного Identity источника пользовательского интерфейса

Чтобы обеспечить полный контроль над Identity пользовательским интерфейсом, запустите шаблон Identity и выберите **переопределить все файлы**.

В следующем выделенном коде показаны изменения для замены пользовательского интерфейса по умолчанию Identity Identity в веб-приложении ASP.NET Core 2,1. Это может потребоваться для полного контроля над Identity пользовательским интерфейсом.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Значение по умолчанию Identity заменяется в следующем коде:

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

## <a name="password-configuration"></a>Настройка пароля

Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity . `InputModel``Password`Свойства находятся в следующих файлах:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Отключить страницу регистрации

Чтобы отключить регистрацию пользователей, выполните следующие действия.

* Шаблон Identity . Включить учетную запись. регистрация, учетная запись. Login и Account. Регистерконфирматион. Например:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Обновите *области или Identity /Пажес/аккаунт/регистер.кштмл.КС* , чтобы пользователи не могли зарегистрироваться из этой конечной точки:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* , чтобы они соответствовали предыдущим изменениям:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Закомментируйте или удалите регистрационную ссылку из *областей/ Identity /Пажес/аккаунт/логин.кштмл*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Обновите страницу *области или Identity /Пажес/аккаунт/регистерконфирматион* .

  * Удалите код и ссылки из файла CSHTML.
  * Удалите код подтверждения из `PageModel` :

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
