---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server
author: guardrex
description: Создание нового размещенного приложения Blazor с проверкой подлинности в Visual Studio с использованием [IdentityServer](https://identityserver.io/) в качестве серверной части
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0fdc88e8e50856fcc4da0beb74f03925ae24401e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103507"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

В этой статье объясняется, как создать новое размещенное приложение Blazor, которое использует [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В Visual Studio сделайте следующее:

1. Создайте новое приложение **Blazor WebAssembly**. Для получения дополнительной информации см. <xref:blazor/get-started>.
1. В диалоговом окне **Создание нового приложения Blazor** в разделе **Проверка подлинности** выберите **Изменить**.
1. Выберите **Учетные записи отдельных пользователей** и нажмите кнопку **ОК**.
1. В разделе **Дополнительно** установите флажок **Размещенный ASP.NET Core**.
1. Нажмите кнопку **Создать**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

Чтобы создать приложение, в командной оболочке выполните следующую команду:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Чтобы указать расположение выходных данных для создания папки проекта, если она не существует, включите в команду параметр выходных данных с путем (например, `-o BlazorSample`). Имя папки также станет частью имени проекта.

---

## <a name="server-app-configuration"></a>Конфигурация серверного приложения

В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.

### <a name="startup-class"></a>Класс Startup

В классе `Startup` представлены следующие дополнения:

* В `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Проверка подлинности с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* В `Startup.Configure`:

  * ПО промежуточного слоя IdentityServer предоставляет конечные точки Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:

    ```csharp
    app.UseAuthentication();
    ```

  * ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core. IdentityServer — это функциональная и расширяемая платформа для устранения проблем с безопасностью приложений. IdentityServer усложняет некоторые наиболее распространенные сценарии. Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки. Если требуется изменить требования к проверке подлинности, можно воспользоваться широкими возможностями IdentityServer для настройки проверки подлинности в соответствии с требованиями приложения.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию. Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы. Кроме того, этот метод:

* Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.
* Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

В `WeatherForecastController` (*Controllers/WeatherForecastController.cs*) к классу применяется атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute). Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу. Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>. Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.

### <a name="applicationdbcontext"></a>ApplicationDbContext

В `ApplicationDbContext` (*Data/ApplicationDbContext.cs*) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer. Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

В `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) конечная точка клиента подготавливается для обслуживания параметров OIDC.

### <a name="app-settings-files"></a>Файл параметров приложения

В файле параметров приложения (*appsettings.json*) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов. В следующем примере есть один клиент. Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth. Профиль указывает на настраиваемый тип приложения. Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).

## <a name="client-app-configuration"></a>Конфигурация клиентского приложения

### <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), приложение автоматически получает ссылку на пакет для [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения. В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Поддержка авторизации API

Поддержка проверки подлинности пользователей подключается к контейнеру службы с помощью метода расширения в составе пакета [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/). Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.

```csharp
builder.Services.AddApiAuthorization();
```

По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`. По соглашению в качестве идентификатора клиента используется имя сборки приложения. Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

Компонент `LoginDisplay` (*Shared/LoginDisplay.razor*) преобразуется для просмотра в компоненте `MainLayout` (*Shared/MainLayout.razor*) и управляет следующими поведениями:

* Для пользователей, прошедших проверку подлинности:
  * Отображает имя текущего пользователя.
  * Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.
  * Отображает кнопку для выхода из приложения.
* Для анонимных пользователей:
  * Предоставляет возможность регистрации.
  * Предоставляет возможность входа в систему.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение из серверного проекта. При использовании Visual Studio выполните одно из следующих действий.

* На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.
* В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.

## <a name="name-and-role-claim-with-api-authorization"></a>Утверждение имени и роли с помощью авторизации API

### <a name="custom-user-factory"></a>Настраиваемая фабрика пользователей

В клиентском приложении создайте настраиваемую фабрику пользователей. Identity Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`. Одна роль отправляется как строковое значение в утверждении. Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

В клиентском приложении зарегистрируйте фабрику в `Program.Main` (*Program.cs*):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

В серверном приложении вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Настройка Identity Server

Воспользуйтесь **одним** из перечисленных ниже подходов.

* [Параметры авторизации API](#api-authorization-options)
* [Служба профилей](#profile-service)

#### <a name="api-authorization-options"></a>Параметры авторизации API

В серверном приложении:

* Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.
* Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Служба профилей

В серверном приложении создайте реализацию `ProfileService`.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

В приложении сервера зарегистрируйте службу профиля в `Startup.ConfigureServices`:

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Использование механизмов авторизации

На этом этапе подходы с авторизацией компонентов в клиентском приложении являются функциональными. Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:

* [Компонент AuthorizeView](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)
* [Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)
* [Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)

  Поддерживается тестирование с использованием нескольких ролей:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

Для `User.Identity.Name` в клиентском приложении указывается имя пользователя, которое обычно является адресом электронной почты для входа.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Развертывание в Службе приложений Azure](xref:security/authentication/identity/spa#deploy-to-production)
* [Импорт сертификата из Key Vault (документация Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
