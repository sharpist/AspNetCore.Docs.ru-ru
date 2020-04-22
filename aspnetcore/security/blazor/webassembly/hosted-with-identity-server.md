---
title: Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью Identity Server
author: guardrex
description: Для создания Blazor нового размещенного приложения с аутентификацией внутри Visual Studio, используюшего бэкэнд [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791614"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью Identity Server

[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Для создания Blazor нового размещенного приложения в Visual Studio, которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API:

1. Используйте Visual Studio ** Blazor ** для создания нового приложения WebAssembly. Для получения дополнительной информации см. <xref:blazor/get-started>.
1. В **журнале Blazor «Создание нового диалога приложений»** выберите **«Изменение** в разделе **аутентификация».**
1. Выберите **индивидуальные учетные записи пользователей,** за которыми следует **OK**.
1. Выберите **ASP.NET Core размещает** флажок в разделе **Advanced.**
1. Нажмите кнопку **Создать**.

Чтобы создать приложение в командной оболочке, выполните следующую команду:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`). Имя папки также становится частью названия проекта.

## <a name="server-app-configuration"></a>Конфигурация приложения сервера

В следующих разделах описаны дополнения к проекту при включении поддержки аутентификации.

### <a name="startup-class"></a>Класс Startup

Класс `Startup` имеет следующие дополнения:

* В `Startup.ConfigureServices`:

  * Удостоверение:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer с <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> дополнительным методом помощника, который настраивает некоторые ASP.NET основных конвенций по умолчанию поверх IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Аутентификация <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> с помощью дополнительного метода помощника, который настраивает приложение для проверки токенов JWT, производимых IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* В `Startup.Configure`:

  * Промежуточная проверка подлинности, которая отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:

    ```csharp
    app.UseAuthentication();
    ```

  * Промежуточное программное обеспечение IdentityServer, которое предоставляет конечные точки Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>АддапиАвторизация

Метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> помощника настраивает [IdentityServer](https://identityserver.io/) для ASP.NET основных сценариев. IdentityServer — это мощная и расширяемая платформа для решения проблем безопасности приложений. IdentityServer предоставляет ненужные сложности для наиболее распространенных сценариев. Следовательно, набор конвенций и параметров конфигурации предоставляется, что мы считаем хорошей отправной точкой. После изменения потребности в аутентификации вся мощь IdentityServer по-прежнему доступна для настройки аутентификации в соответствии с требованиями приложения.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> помощника настраивает схему политики для приложения в качестве обработчика аутентификации по умолчанию. Политика настроена таким образом, чтобы identity обрабатывал все запросы, `/Identity`направимые на любой подпуть в пространстве URL-адреса Identity. Обрабатывает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> все остальные запросы. Кроме того, этот метод:

* Регистрирует ресурс `{APPLICATION NAME}API` API с IdentityServer с `{APPLICATION NAME}API`областью по умолчанию.
* Настраивает JWT Bearer Token Middleware для проверки токенов, выпущенных IdentityServer для приложения.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

В `WeatherForecastController` *(Controllers/WeatherForecastController.cs)* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу. Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию для доступа к ресурсу. Политика авторизации по умолчанию настроена на использование схемы <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> проверки подлинности по умолчанию, которая настроена на схему политики, упомянутую ранее. Метод помощника настраивается <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> как обработчик по умолчанию для запросов в приложение.

### <a name="applicationdbcontext"></a>ApplicationDbContext

В `ApplicationDbContext` (*Data/ApplicationDbContext.cs),* <xref:Microsoft.EntityFrameworkCore.DbContext> то же самое используется в Identity, за исключением того, что он распространяется <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> на включение схемы для IdentityServer. Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Чтобы получить полный контроль над схемой базы данных, наследуете от одного из доступных классов identity <xref:Microsoft.EntityFrameworkCore.DbContext> и назначайте контекст, чтобы включить схему идентификации, вызывая `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в `OnModelCreating` методе.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

В `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* конечная точка клиента предназначена для обслуживания параметров OIDC.

### <a name="app-settings-files"></a>Файлы настроек приложения

В файле настроек приложения *(appsettings.json)* `IdentityServer` в корне проекта раздел описывает список настроенных клиентов. В следующем примере есть один клиент. Имя клиента соответствует названию приложения и отображается `ClientId` по конвенции к параметру OAuth. Профиль указывает на настроенный тип приложения. Профиль используется внутренне для управления конюмитами, упрощающие процесс настройки сервера. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

В файле настроек среды разработки *(приложения. Development.json*) в корне `IdentityServer` проекта, в разделе описывается ключ, используемый для подписания токенов. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Конфигурация клиентского приложения

### <a name="authentication-package"></a>Пакет аутентификации

Когда приложение создается для использования`Individual`индивидуальных учетных записей пользователей (), приложение автоматически получает ссылку на `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет для пакета в файле проекта приложения. Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.

При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.

### <a name="api-authorization-support"></a>Поддержка авторизации API

Поддержка аутентификации пользователей подключается к сервисному контейнеру методом расширения, предусмотренным `Microsoft.AspNetCore.Components.WebAssembly.Authentication` внутри пакета. Этот метод настраивает все службы, необходимые для взаимодействия приложения с существующей системой авторизации.

```csharp
builder.Services.AddApiAuthorization();
```

По умолчанию он загружает конфигурацию `_configuration/{client-id}`для приложения по конвенции от . По конвенции идентификатор клиента устанавливается на имя сборки приложения. Этот URL может быть изменен, чтобы указать на отдельную конечную точку, вызовив перегрузку с опциями.

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>ПеренаправлениеКомпонентToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

Компонент `LoginDisplay` *(Общий/LoginDisplay.razor)* отображается `MainLayout` в компоненте *(Общий/MainLayout.razor)* и управляет следующим поведением:

* Для аутентифицированных пользователей:
  * Отображает текущее имя пользователя.
  * Предлагает ссылку на страницу профиля пользователя в ASP.NET Core Identity.
  * Предлагает кнопку для выхода из приложения.
* Для анонимных пользователей:
  * Предлагает возможность регистрации.
  * Предлагает возможность входа в систему.

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

### <a name="authentication-component"></a>Компонент аутентификации

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Выполнить приложение из проекта Server. При использовании Visual Studio выберите проект Server в **Solution Explorer** и выберите кнопку **Run** в панели инструментов или запустите приложение из меню **Debug.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Запрос дополнительных токенов доступа](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
