---
title: Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 6f049906da4a1aa87f293f5ad1af19dad44f477a
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150034"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory B2C

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

В этой статье описывается, как создать Blazor автономное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Регистрация приложений в AAD B2C и создание решения

### <a name="create-a-tenant"></a>Создание клиента

Следуйте указаниям в [руководстве по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания клиента AAD B2C и записи следующих сведений.

* AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`, включающий косую черту)
* Домен клиента AAD B2C (например, `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>Регистрация приложения API сервера

Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , чтобы зарегистрировать приложение AAD для *приложения API сервера* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:

1. Выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, ** Blazor AAD B2C сервера**).
1. Для **поддерживаемых типов учетных записей**выберите **учетные записи в любом организационном каталоге или любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.** (несколько клиентов) для этого интерфейса.
1. В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.
1. Убедитесь, что **разрешения** > **на предоставление разрешений администратора OpenID Connect и offline_access** включены.
1. Выберите **Зарегистрировать**.

В **предоставление API**:

1. Выберите **Добавить область**.
1. Выберите **Сохранить и продолжить**.
1. Укажите **имя области** (например, `API.Access`).
1. Укажите **Отображаемое имя согласия администратора** (например, `Access API`).
1. Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).
1. Убедитесь, что для **состояния** задано значение **включено**.
1. Выберите **Добавить область**.

Запишите следующие сведения:

* *Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)
* URI идентификатора приложения (например `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, или предоставленное вами пользовательское значение)
* Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222`)
* *Приложение API сервера* URI идентификатора приложения (например `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, портал Azure может по умолчанию присвоить значение идентификатору клиента)
* Область по умолчанию (например `API.Access`,)

### <a name="register-a-client-app"></a>Регистрация клиентского приложения

Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) еще раз, чтобы зарегистрировать приложение AAD для *клиентского приложения* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:

1. Выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, ** Blazor AAD B2C клиента**).
1. Для **поддерживаемых типов учетных записей**выберите **учетные записи в любом организационном каталоге или любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.** (несколько клиентов) для этого интерфейса.
1. Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.
1. Убедитесь, что **разрешения** > **на предоставление разрешений администратора OpenID Connect и offline_access** включены.
1. Выберите **Зарегистрировать**.

В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:

1. Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.
1. Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.
1. Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.
1. Нажмите кнопку **Сохранить**.

В **разрешениях API**:

1. Убедитесь, что приложение имеет **Microsoft Graph** > **пользователь. чтение** .
1. Выберите **Добавить разрешение** , а затем — **Мои API**.
1. Выберите *приложение API сервера* из столбца **имя** (например, ** Blazor Server AAD B2C**).
1. Откройте список **API** .
1. Разрешение доступа к API (например, `API.Access`).
1. Выберите **Добавить разрешения**.
1. Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** . Выберите **Да** для подтверждения.

В **домашних** > **Azure AD B2C** > **пользовательских потоках**:

[Создание потока пользователя для регистрации и входа в систему](/azure/active-directory-b2c/tutorial-create-user-flows)

Для заполнения `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя " > **Отображаемое имя** **утверждения приложения**".

Запишите следующие сведения:

* Запишите идентификатор приложения *клиентского приложения* (идентификатор клиента) (например, `33333333-3333-3333-3333-333333333333`).
* Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Создайте приложение

Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`). Имя папки также станет частью имени проекта.

> [!NOTE]
> Передайте универсальный код ресурса (URI `app-id-uri` ) идентификатора приложения в параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .

## <a name="server-app-configuration"></a>Конфигурация серверного приложения

*Этот раздел относится к **серверному** приложению решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-интерфейсов API обеспечивается `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

`AddAuthentication` Метод настраивает службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию. `AddAzureADB2CBearer` Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`и `UseAuthorization` убедитесь, что:

* Приложение пытается проанализировать и проверить маркеры в входящих запросах.
* Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

По умолчанию значение `User.Identity.Name` не заполняется.

Чтобы настроить приложение для получения значения `name` из типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Параметры приложения

Файл *appSettings. JSON* содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Контроллер Веасерфорекаст

Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с `[Authorize]` атрибутом, примененным к контроллеру. **Важно** понимать, что:

* `[Authorize]` Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.
* `[Authorize]` Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Конфигурация клиентского приложения

*Этот раздел относится к **клиентскому** приложению решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

При создании приложения для использования отдельной учетной записи B2C (`IndividualB2C`) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.

`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Добавлена поддержка `HttpClient` экземпляров, включающая маркеры доступа при выполнении запросов к серверному проекту.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом. Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.

Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Пример.

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a>Области токенов доступа

Области токена доступа по умолчанию представляют собой список областей токенов доступа.

* Включается по умолчанию в запросе на вход.
* Используется для предоставления маркера доступа сразу после проверки подлинности.

Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory. При необходимости можно добавить дополнительные области для дополнительных приложений API:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел. Например, портал Azure может предоставить один из следующих форматов URI области:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Укажите URI области без схемы и узла:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :

* [Запрос дополнительных маркеров доступа](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Присоединение маркеров к исходящим запросам](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент Редиректтологин

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент Логиндисплай

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение из серверного проекта. При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Руководство по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
