---
title: Обеспечение безопасности размещенного в ASP.NET Core Blazor сборки приложения Identity с помощью сервера
author: guardrex
description: Создание нового Blazor размещенного приложения с проверкой подлинности в Visual Studio, использующей серверную часть [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: bf2298618e922df412e0742177afd390c4116388
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768131"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Обеспечение безопасности размещенного в ASP.NET Core Blazor сборки приложения Identity с помощью сервера

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Чтобы создать новое Blazor размещенное приложение в Visual Studio, использующее [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API:

1. Создайте новое ** Blazor приложение сборки** с помощью Visual Studio. Для получения дополнительной информации см. <xref:blazor/get-started>.
1. В диалоговом окне **Создание Blazor нового приложения** выберите **изменить** в разделе **Проверка подлинности** .
1. Выберите **учетные записи отдельных пользователей** , а затем нажмите **кнопку ОК**.
1. Установите флажок **ASP.NET Core размещено** в разделе **Дополнительно** .
1. Нажмите кнопку **Создать**.

Чтобы создать приложение в командной оболочке, выполните следующую команду:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`). Имя папки также станет частью имени проекта.

## <a name="server-app-configuration"></a>Конфигурация серверного приложения

В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.

### <a name="startup-class"></a>Класс Startup

`Startup` Класс имеет следующие дополнения:

* В `Startup.ConfigureServices`:

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer с дополнительным <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> вспомогательным методом, который настраивает некоторые соглашения ASP.NET Core по умолчанию поверх IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Проверка подлинности <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> с помощью дополнительного вспомогательного метода, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* В `Startup.Configure`:

  * По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:

    ```csharp
    app.UseAuthentication();
    ```

  * По промежуточного слоя IdentityServer, которое предоставляет конечные точки Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * По промежуточного слоя для проверки подлинности и авторизации:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>аддапиаусоризатион

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Вспомогательный метод настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core. IdentityServer — это мощная и расширяемая платформа для обработки проблем безопасности приложений. IdentityServer предоставляет ненужную сложность для наиболее распространенных сценариев. Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые мы рассмотрим хорошей отправной точкой. После изменения требований к проверке подлинности все возможности IdentityServer по-прежнему доступны для настройки проверки подлинности в соответствии с требованиями приложения.

### <a name="addidentityserverjwt"></a>аддидентитисервержвт

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию. Политика настроена на разрешение Identity обработки всех запросов, направляемых по любому вложенному пути в пространстве Identity `/Identity`URL-адресов. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Обрабатывает все остальные запросы. Кроме того, этот метод:

* Регистрирует ресурс `{APPLICATION NAME}API` API в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.
* Настраивает промежуточное по для токена носителя JWT для проверки маркеров, выданных IdentityServer для приложения.

### <a name="weatherforecastcontroller"></a>веасерфорекастконтроллер

В `WeatherForecastController` (*Controllers/веасерфорекастконтроллер. CS*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу. Атрибут указывает, что пользователь должен быть санкционирован на основании политики по умолчанию для доступа к ресурсу. Политика авторизации по умолчанию настроена для использования схемы проверки подлинности по умолчанию, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> которая настроена в схеме политики, упомянутой ранее. Вспомогательный метод настраивается в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> качестве обработчика по умолчанию для запросов к приложению.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` В (*Data/ApplicationDbContext. CS*) <xref:Microsoft.EntityFrameworkCore.DbContext> используется Identity то же, что и исключение, которое расширяется <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer. Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных Identity <xref:Microsoft.EntityFrameworkCore.DbContext> классов и настройте контекст для включения Identity схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.

### <a name="oidcconfigurationcontroller"></a>оидкконфигуратионконтроллер

В `OidcConfigurationController` (*Controllers/оидкконфигуратионконтроллер. CS*) конечная точка клиента подготавливается для обслуживания параметров OIDC.

### <a name="app-settings-files"></a>Файлы параметров приложения

В файле параметров приложения (*appSettings. JSON*) в корне проекта в `IdentityServer` разделе описывается список настроенных клиентов. В следующем примере есть один клиент. Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром OAuth `ClientId` . Профиль указывает на настраиваемый тип приложения. Профиль используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Конфигурация клиентского приложения

### <a name="authentication-package"></a>Пакет проверки подлинности

При создании приложения для использования отдельных учетных записей`Individual`пользователей приложение автоматически получает ссылку `Microsoft.AspNetCore.Components.WebAssembly.Authentication` на пакет в файле проекта приложения. Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.

### <a name="api-authorization-support"></a>Поддержка авторизации API

Поддержка проверки подлинности пользователей подключается к контейнеру службы с помощью метода расширения, предоставляемого `Microsoft.AspNetCore.Components.WebAssembly.Authentication` в пакете. Этот метод настраивает все службы, необходимые для взаимодействия приложения с существующей системой авторизации.

```csharp
builder.Services.AddApiAuthorization();
```

По умолчанию конфигурация приложения загружается по соглашению из `_configuration/{client-id}`. По соглашению идентификатор клиента устанавливается в имя сборки приложения. Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент Редиректтологин

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент Логиндисплай

Компонент (*Shared/логиндисплай. Razor*) подготавливается к `MainLayout` просмотру в компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями: `LoginDisplay`

* Для пользователей, прошедших проверку подлинности:
  * Отображает имя текущего пользователя.
  * Содержит ссылку на страницу профиля пользователя в ASP.NET Core Identity.
  * Предлагает кнопку для выхода из приложения.
* Для анонимных пользователей:
  * Предлагает возможность регистрации.
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

Запустите приложение из серверного проекта. При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/blazor/webassembly/additional-scenarios>
