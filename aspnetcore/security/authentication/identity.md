---
title: 'Введение в :::no-loc(Identity)::: ASP.NET Core'
author: rick-anderson
description: 'Используйте :::no-loc(Identity)::: с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445439"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Введение в :::no-loc(Identity)::: ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core :::no-loc(Identity)::::

* — Это API, поддерживающий функцию входа в пользовательский интерфейс.
* Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.

Пользователи могут создать учетную запись с данными входа, хранящимися в, :::no-loc(Identity)::: или использовать внешний поставщик входа. Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).

[ :::no-loc(Identity)::: Исходный код](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) доступен на сайте GitHub. [Формирование :::no-loc(Identity)::: шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном :::no-loc(Identity)::: .

:::no-loc(Identity):::обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей. Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.

В этом разделе вы узнаете, как использовать :::no-loc(Identity)::: для регистрации, входа и выхода пользователя. Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей. Более подробные инструкции по созданию приложений, использующих :::no-loc(Identity)::: , см. в разделе [дальнейшие действия](#next).

[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :

* Развитие платформы разработки Azure Active Directory (Azure AD).
* Не связано с ASP.NET Core :::no-loc(Identity)::: .

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Создание веб-приложения с проверкой подлинности

Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выберите **файл** > **создать** > **проект**.
* Выберите **Новое веб-приложение ASP.NET Core**. Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект. Нажмите кнопку **ОК**.
* Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.
* Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Предыдущая команда создает :::no-loc(Razor)::: веб-приложение с помощью SQLite. Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Созданный проект предоставляет [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) в виде [ :::no-loc(Razor)::: библиотеки классов](xref:razor-pages/ui-class). :::no-loc(Identity)::: :::no-loc(Razor)::: Библиотека классов предоставляет конечные точки с `:::no-loc(Identity):::` областью. Пример:

* /:::no-loc(Identity):::/аккаунт/логин
* /:::no-loc(Identity):::/аккаунт/логаут
* /:::no-loc(Identity):::/аккаунт/манаже

### <a name="apply-migrations"></a>Применение миграции

Примените миграции, чтобы инициализировать базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующую команду в консоли диспетчера пакетов (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Миграция не требуется на этом этапе при использовании SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Для LocalDB выполните следующую команду:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Проверка регистра и вход в систему

Запустите приложение и зарегистрируйте пользователя. В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Настройка :::no-loc(Identity)::: служб

Службы добавляются в `ConfigureServices` . По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

Выделенный выше код настраивается :::no-loc(Identity)::: со значениями параметров по умолчанию. Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::включается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data). `app.UseAuthorization`включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию. `UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.

Дополнительные сведения о `:::no-loc(Identity):::Options` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> и [запуске приложения](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Регистр шаблонов, вход, выход и Регистерконфирматион

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Добавьте `Register` файлы, `Login` , `LogOut` и `RegisterConfirmation` . Соблюдайте [идентификатор шаблона в :::no-loc(Razor)::: проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы создали проект с именем " **APP1**", выполните следующие команды. В противном случае используйте правильное пространство имен для `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.

Дополнительные сведения о формировании шаблонов :::no-loc(Identity)::: см. в статье [удостоверение шаблона в :::no-loc(Razor)::: проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Проверка регистра

Когда пользователь нажимает кнопку **Register** на `Register` странице, `RegisterModel.OnPostAsync` вызывается действие. Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Вход в систему

Форма входа отображается в следующих случаях:

* Выбрана ссылка для **входа** .
* Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.

При отправке формы на странице входа `OnPostAsync` вызывается действие. `PasswordSignInAsync`метод вызывается для `_signInManager` объекта.

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .

### <a name="log-out"></a>выход из системы;

Ссылка **выхода** вызывает `LogoutModel.OnPost` действие. 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.

[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.

Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Проверьте:::no-loc(Identity):::

Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам. Чтобы протестировать :::no-loc(Identity)::: , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** . Вы перейдете на страницу входа.

### <a name="explore-no-locidentity"></a>Просматриваем:::no-loc(Identity):::

:::no-loc(Identity):::Более подробное изучение:

* [Создание полного источника идентификатора пользовательского интерфейса](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Изучите источник каждой страницы и пошаговое выполнение отладчика.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Компонента

Все :::no-loc(Identity)::: зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Основным пакетом для :::no-loc(Identity)::: является [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/) Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в :::no-loc(Identity)::: состав `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Переход на ASP.NET Core:::no-loc(Identity):::

Дополнительные сведения и рекомендации по переносу существующего :::no-loc(Identity)::: хранилища см. в статье [Миграция :::no-loc(Identity)::: проверки подлинности и ](xref:migration/identity).

## <a name="setting-password-strength"></a>Установка силы пароля

См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>Адддефаулт :::no-loc(Identity)::: и добавить:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>было введено в ASP.NET Core 2,1. Вызов `AddDefault:::no-loc(Identity):::` аналогичен вызову следующего:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Дополнительные сведения см. в разделе [адддефаулт :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Запретить публикацию статических :::no-loc(Identity)::: ресурсов

Чтобы предотвратить публикацию статических :::no-loc(Identity)::: ресурсов (таблиц стилей и файлов JavaScript для :::no-loc(Identity)::: пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `Remove:::no-loc(Identity):::Assets` целевой объект в файл проекта приложения:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Следующие шаги

* [:::no-loc(Identity):::Исходный код ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .
* [Настройка :::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core :::no-loc(Identity)::: — это система членства, которая добавляет функции входа в ASP.NET Core приложения. Пользователи могут создать учетную запись с данными входа, хранящимися в, :::no-loc(Identity)::: или использовать внешний поставщик входа. Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).

:::no-loc(Identity):::можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей. Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.

[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).

В этом разделе вы узнаете, как использовать :::no-loc(Identity)::: для регистрации, входа и выхода пользователя. Более подробные инструкции по созданию приложений, использующих :::no-loc(Identity)::: , см. в разделе дальнейшие действия в конце этой статьи.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>Адддефаулт :::no-loc(Identity)::: и добавить:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>было введено в ASP.NET Core 2,1. Вызов `AddDefault:::no-loc(Identity):::` аналогичен вызову следующего:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Дополнительные сведения см. в разделе [адддефаулт :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Создание веб-приложения с проверкой подлинности

Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выберите **файл** > **создать** > **проект**.
* Выберите **Новое веб-приложение ASP.NET Core**. Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект. Нажмите кнопку **ОК**.
* Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.
* Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Созданный проект предоставляет [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) в виде [ :::no-loc(Razor)::: библиотеки классов](xref:razor-pages/ui-class). :::no-loc(Identity)::: :::no-loc(Razor)::: Библиотека классов предоставляет конечные точки с `:::no-loc(Identity):::` областью. Пример:

* /:::no-loc(Identity):::/аккаунт/логин
* /:::no-loc(Identity):::/аккаунт/логаут
* /:::no-loc(Identity):::/аккаунт/манаже

### <a name="apply-migrations"></a>Применение миграции

Примените миграции, чтобы инициализировать базу данных.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующую команду в консоли диспетчера пакетов (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Проверка регистра и вход в систему

Запустите приложение и зарегистрируйте пользователя. В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Настройка :::no-loc(Identity)::: служб

Службы добавляются в `ConfigureServices` . По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Приведенный выше код настраивается :::no-loc(Identity)::: со значениями параметров по умолчанию. Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Дополнительные сведения см. в статье [ :::no-loc(Identity)::: класс Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Регистрация, вход и выход из шаблона формирования шаблонов

Соблюдайте [идентификатор шаблона в :::no-loc(Razor)::: проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Добавьте файлы Register, login и LogOut.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы создали проект с именем " **APP1**", выполните следующие команды. В противном случае используйте правильное пространство имен для `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.

---

### <a name="examine-register"></a>Проверка регистра

Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие. Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .

**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Вход в систему

Форма входа отображается в следующих случаях:

* Выбрана ссылка для **входа** .
* Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.

При отправке формы на странице входа `OnPostAsync` вызывается действие. `PasswordSignInAsync`метод вызывается для `_signInManager` объекта.

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .

### <a name="log-out"></a>выход из системы;

Ссылка **выхода** вызывает `LogoutModel.OnPost` действие. 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.

Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Проверьте:::no-loc(Identity):::

Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам. Чтобы протестировать :::no-loc(Identity)::: , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** . Вы перейдете на страницу входа.

### <a name="explore-no-locidentity"></a>Просматриваем:::no-loc(Identity):::

:::no-loc(Identity):::Более подробное изучение:

* [Создание полного источника идентификатора пользовательского интерфейса](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Изучите источник каждой страницы и пошаговое выполнение отладчика.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Компонента

Все :::no-loc(Identity)::: зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).

Основным пакетом для :::no-loc(Identity)::: является [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/) Этот пакет содержит основной набор интерфейсов для ASP.NET Core и входит в :::no-loc(Identity)::: состав `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Переход на ASP.NET Core:::no-loc(Identity):::

Дополнительные сведения и рекомендации по переносу существующего :::no-loc(Identity)::: хранилища см. в статье [Миграция :::no-loc(Identity)::: проверки подлинности и ](xref:migration/identity).

## <a name="setting-password-strength"></a>Установка силы пароля

См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.

## <a name="next-steps"></a>Следующие шаги

* Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .
* [Настройка :::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
