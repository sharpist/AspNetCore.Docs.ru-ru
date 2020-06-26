---
title: Миграция проверки подлинности и Identity в ASP.NET Core 2,0
author: scottaddie
description: В этой статье описаны наиболее распространенные шаги для миграции ASP.NET Core 1. x и Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: dacf6fa7191f51f36b9ba65a90746a26f958fc03
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408673"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Миграция проверки подлинности и Identity в ASP.NET Core 2,0

[Скотт Эдди (](https://github.com/scottaddie) и [Хао кунг](https://github.com/HaoK)

ASP.NET Core 2,0 имеет новую модель для проверки подлинности и [Identity](xref:security/authentication/identity) упрощает настройку с помощью служб. ASP.NET Core 1. x приложений, использующих проверку подлинности, или Identity можно обновить для использования новой модели, как описано ниже.

## <a name="update-namespaces"></a>Обновить пространства имен

В 1. x классы, такие `IdentityRole` и, `IdentityUser` были найдены в `Microsoft.AspNetCore.Identity.EntityFrameworkCore` пространстве имен.

В 2,0 <xref:Microsoft.AspNetCore.Identity> пространство имен стало новым доменом для нескольких таких классов. В коде по умолчанию Identity затронутые классы включают `ApplicationUser` и `Startup` . Настройте `using` инструкции для разрешения затронутых ссылок.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Промежуточное по проверки подлинности и службы

В проектах 1. x Проверка подлинности настраивается через по промежуточного слоя. Для каждой схемы проверки подлинности, которую вы хотите поддерживать, вызывается метод по промежуточного слоя.

Следующий пример 1. x настраивает проверку подлинности Facebook с помощью Identity в *Startup.CS*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

В проектах 2,0 проверка подлинности настраивается с помощью служб. Каждая схема проверки подлинности регистрируется в `ConfigureServices` методе *Startup.CS*. `UseIdentity`Метод заменяется на `UseAuthentication` .

Следующий пример 2,0 настраивает проверку подлинности Facebook с помощью Identity в *Startup.CS*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

Этот `UseAuthentication` метод добавляет один компонент промежуточного слоя проверки подлинности, отвечающий за автоматическую проверку подлинности и обработку запросов удаленной проверки подлинности. Он заменяет все отдельные компоненты по промежуточного слоя одним общим компонентом по промежуточного слоя.

Ниже приведены инструкции по миграции для каждой основной схемы проверки подлинности 2,0.

### <a name="cookie-based-authentication"></a>Проверка подлинности на основе файлов cookie

Выберите один из двух параметров ниже и внесите необходимые изменения в *Startup.CS*:

1. Использование файлов cookie сIdentity
    - Замените `UseIdentity` на `UseAuthentication` в `Configure` методе:

        ```csharp
        app.UseAuthentication();
        ```

    - Вызовите `AddIdentity` метод в `ConfigureServices` методе, чтобы добавить службы проверки подлинности файлов cookie.
    - При необходимости вызовите `ConfigureApplicationCookie` метод или `ConfigureExternalCookie` в `ConfigureServices` методе, чтобы настроить Identity Параметры файла cookie.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Использование файлов cookie безIdentity
    - Замените `UseCookieAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - Вызовите `AddAuthentication` `AddCookie` методы и в `ConfigureServices` методе:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Проверка подлинности носителя JWT

Внесите следующие изменения в *Startup.CS*:
- Замените `UseJwtBearerAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddJwtBearer` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Этот фрагмент кода не используется Identity , поэтому схему по умолчанию следует задать, передав `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` методу.

### <a name="openid-connect-oidc-authentication"></a>Проверка подлинности OpenID Connect Connect (OIDC)

Внесите следующие изменения в *Startup.CS*:

- Замените `UseOpenIdConnectAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddOpenIdConnect` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Замените `PostLogoutRedirectUri` свойство в `OpenIdConnectOptions` действии на `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Проверка подлинности Facebook

Внесите следующие изменения в *Startup.CS*:
- Замените `UseFacebookAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddFacebook` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Проверка подлинности Google

Внесите следующие изменения в *Startup.CS*:
- Замените `UseGoogleAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddGoogle` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Аутентификация учетной записи Майкрософт

Дополнительные сведения о учетная запись Майкрософт проверки подлинности см. в [этой статье о проблемах в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

Внесите следующие изменения в *Startup.CS*:
- Замените `UseMicrosoftAccountAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddMicrosoftAccount` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Проверка подлинности Twitter

Внесите следующие изменения в *Startup.CS*:
- Замените `UseTwitterAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Вызовите `AddTwitter` метод в `ConfigureServices` методе:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Настройка схем проверки подлинности по умолчанию

В 1. x `AutomaticAuthenticate` `AutomaticChallenge` Свойства и базового класса [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) предназначены для установки в одной схеме проверки подлинности. Не существовало хорошего способа принудительного применения этого.

В 2,0 эти два свойства были удалены как свойства в отдельном `AuthenticationOptions` экземпляре. Их можно настроить в `AddAuthentication` вызове метода в `ConfigureServices` методе *Startup.CS*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

В предыдущем фрагменте кода схема по умолчанию имеет значение `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").

Кроме того, можно использовать перегруженную версию метода, `AddAuthentication` чтобы задать более одного свойства. В следующем примере перегруженного метода схема по умолчанию имеет значение `CookieAuthenticationDefaults.AuthenticationScheme` . Схема проверки подлинности также может быть указана в индивидуальных `[Authorize]` атрибутах или политиках авторизации.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Определите схему по умолчанию в 2,0, если выполняется одно из следующих условий.
- Вы хотите, чтобы пользователь автоматически вошел в учетную запись
- Использование `[Authorize]` политик атрибутов или авторизации без указания схем

Исключением из этого правила является `AddIdentity` метод. Этот метод добавляет файлы cookie для вас и устанавливает схемы проверки подлинности и вызова по умолчанию в файл cookie приложения `IdentityConstants.ApplicationScheme` . Кроме того, он задает схему входа по умолчанию для внешнего файла cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Использование расширений проверки подлинности HttpContext

`IAuthenticationManager`Интерфейс является основной точкой входа в систему проверки подлинности 1. x. Он был заменен новым набором `HttpContext` методов расширения в `Microsoft.AspNetCore.Authentication` пространстве имен.

Например, проекты 1. x ссылаются на `Authentication` свойство:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

В проектах 2,0 импортируйте `Microsoft.AspNetCore.Authentication` пространство имен и удалите ссылки на `Authentication` Свойства:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Проверка подлинности Windows (HTTP.sys/IISIntegration)

Существует два варианта проверки подлинности Windows:

* Узел допускает только пользователей, прошедших проверку подлинности. На этот вариант не влияют изменения 2,0.
* Узел допускает анонимные и прошедшие проверку подлинности пользователи. На этот вариант влияют изменения 2,0. Например, приложение должно разрешать анонимных пользователей на уровне [IIS](xref:host-and-deploy/iis/index) или [HTTP.sys](xref:fundamentals/servers/httpsys) , но авторизовать пользователей в контроллере. В этом сценарии задайте схему по умолчанию в `Startup.ConfigureServices` методе.

  Для [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)задайте для схемы по умолчанию `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Для [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)задайте для схемы по умолчанию `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Если не задать схему по умолчанию, запрос авторизации (запрос) не будет работать со следующим исключением:

  > `System.InvalidOperationException`: Не указан Аусентикатионсчеме, и Дефаултчалленжесчеме не найден.

Для получения дополнительной информации см. <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Экземпляры Идентитикукиеоптионс

Побочным результатом изменений 2,0 является переключение на использование именованных параметров вместо экземпляров параметров файлов cookie. Возможность настройки Identity имен для схемы файлов cookie удаляется.

Например, проекты 1. x используют [внедрение конструктора](xref:mvc/controllers/dependency-injection#constructor-injection) для передачи `IdentityCookieOptions` параметра в *AccountController.CS* и *ManageController.CS*. Доступ к схеме проверки подлинности внешних файлов cookie осуществляется из предоставленного экземпляра:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Вышеупомянутый внедренный конструктор не нужен в проектах 2,0, и `_externalCookieScheme` поле можно удалить:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

в проектах 1. x используется `_externalCookieScheme` поле следующим образом:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

В проектах 2,0 замените приведенный выше код следующим. `IdentityConstants.ExternalScheme`Константу можно использовать напрямую.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Разрешите вновь добавленный `SignOutAsync` вызов, импортировав следующее пространство имен:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Добавление свойств навигации Идентитюсер POCO

Свойства навигации ядра (EF) для базового `IdentityUser` объекта POCO (обычный объект CLR) были удалены. Entity Framework Если проект 1. x использовал эти свойства, вручную добавьте их обратно в проект 2,0:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Чтобы предотвратить дублирование внешних ключей при выполнении миграции EF Core, добавьте следующий объект в `IdentityDbContext` `OnModelCreating` метод класса (после `base.OnModelCreating();` вызова):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Заменить Жетекстерналаусентикатионсчемес

Синхронный метод `GetExternalAuthenticationSchemes` был удален с предпочтением асинхронной версии. проекты 1. x имеют следующий код в *Controllers/манажеконтроллер. CS*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Этот метод отображается в *представлениях, учетной записи или имени входа. cshtml* :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

В проектах 2,0 используйте <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> метод. Изменение в *ManageController.CS* напоминает следующий код:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

В *Login. cshtml*свойство, `AuthenticationScheme` доступ к которому осуществляется в `foreach` цикле, изменяется на `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Изменение свойства Манажелогинсвиевмодел

`ManageLoginsViewModel`Объект используется в `ManageLogins` действии *ManageController.CS*. В проектах 1. x `OtherLogins` возвращаемым типом свойства объекта является `IList<AuthenticationDescription>` . Этот тип возвращаемого значения требует импорта `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

В проектах 2,0 тип возвращаемого значения изменяется на `IList<AuthenticationScheme>` . Этот новый тип возвращаемого значения требует замены импорта импортом `Microsoft.AspNetCore.Http.Authentication` `Microsoft.AspNetCore.Authentication` .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в [обсуждении проблемы с проверкой Подлинности 2,0](https://github.com/aspnet/Security/issues/1338) на сайте GitHub.
