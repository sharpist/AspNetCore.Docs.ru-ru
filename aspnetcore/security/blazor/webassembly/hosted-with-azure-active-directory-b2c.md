---
title: Защищайте Blazor ASP.NET Core WebAssembly размещает приложение с Помощью Активного каталога Azure B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123471"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Защищайте Blazor ASP.NET Core WebAssembly размещает приложение с Помощью Активного каталога Azure B2C

[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

В этой статье описывается, как создать автономное приложение Blazor WebAssembly, использующее для проверки подлинности [Azure Active Directory (AAD) B2C.](/azure/active-directory-b2c/overview)

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Регистрация приложений в AAD B2C и создание решения

### <a name="create-a-tenant"></a>Создание клиента

Следуйте инструкциям в [учебном центре: Создайте арендатора Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания арендатора AAD B2C и записи следующей информации:

* AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`который включает в себя задний слэш)
* AAD B2C Арендатор домена `contoso.onmicrosoft.com`(например, )

### <a name="register-a-server-api-app"></a>Регистрация приложения API сервера

Следуйте инструкциям в [учебном центре: Зарегистрируйте приложение в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) для регистрации приложения AAD для *приложения Server API* в зоне**регистрации приложений** **Azure Active Directory** > на портале Azure:

1. Выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, ** Blazor Server AAD B2C).**
1. Для **типов поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организации или любом поставщике идентификационных данных. Для аутентификации пользователей с помощью Azure AD B2C.** (мультитенант) для этого опыта.
1. *Приложение Server API* не требует **перенаправления URI** в этом сценарии, поэтому оставьте упаж вниз в **Веб** и не вводите перенаправление URI.
1. Подтвердите, **Permissions** > что разрешение**на админ-концентратор для openid и offline_access разрешений** включено.
1. Выберите **Зарегистрировать**.

В **экспозиции API**:

1. Выберите **Добавить область**.
1. Выберите **Сохранить и продолжить**.
1. Укажите **имя области** (например, `API.Access`).
1. Укажите **имя отображения согласия админа** (например, `Access API`).
1. Укажите **описание согласия админа** (например, `Allows the app to access server app API endpoints.`).
1. Подтвердите, что **государство** настроено на **включено.**
1. Выберите **область добавления.**

Запишите следующую информацию:

* *Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)
* App ID URI (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`или пользовательское значение, которое вы предоставили)
* Идентификатор каталога (идентификатор арендатора) (например, `222222222-2222-2222-2222-222222222222`)
* *Приложение API сервера* APP ID URI (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`портал Azure может по умолчанию значение для идентификатора клиента)
* Сфера действия по `API.Access`умолчанию (например, )

### <a name="register-a-client-app"></a>Регистрация клиентского приложения

Следуйте инструкциям в [учебном центре: Зарегистрируйте приложение в Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-register-applications) чтобы снова зарегистрировать приложение AAD для *приложения Клиента* в зоне**регистрации приложений** **Azure Active Directory** > на портале Azure:

1. Выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, ** Blazor Client AAD B2C).**
1. Для **типов поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организации или любом поставщике идентификационных данных. Для аутентификации пользователей с помощью Azure AD B2C.** (мультитенант) для этого опыта.
1. Оставьте **перенаправить URI** упасть набор в **Интернет**, `https://localhost:5001/authentication/login-callback`и обеспечить перенаправление URI .
1. Подтвердите, **Permissions** > что разрешение**на админ-концентратор для openid и offline_access разрешений** включено.
1. Выберите **Зарегистрировать**.

В**настройках платформы** >  **аутентификации** > **Web**:

1. Подтвердите, `https://localhost:5001/authentication/login-callback` что **redirect URI** присутствует.
1. Для **неявного гранта**выберите флажки для **токенов Access** и **токенов ID.**
1. Остальные по умолчанию для приложения приемлемы для этого опыта.
1. Нажмите кнопку **Сохранить**.

В **разрешениях API:**

1. Подтвердите, что приложение имеет разрешение **Microsoft Graph** > **User.Read.**
1. Выберите **Добавить разрешение,** за которым следуют **мои AA.**
1. Выберите *приложение Server API* из столбца **«Имя»** (например, ** Blazor Server AAD B2C).**
1. Откройте список **API.**
1. Включить доступ к API (например, `API.Access`).
1. Выберите **Добавить разрешения**.
1. Выберите **содержимое администратора Гранта для кнопки «TENANT NAME».** Выберите **Да** для подтверждения.

В **домашних** > **Azure AD B2C** > **Потоки пользователей:**

[Создание потока пользователя для регистрации и входа в систему](/azure/active-directory-b2c/tutorial-create-user-flows)

Как минимум, выберите атрибут **приложения Претензии** > **Display Name** для заполнения `context.User.Identity.Name` `LoginDisplay` компонента *(Общий/LoginDisplay.razor*).

Запишите следующую информацию:

* Запись идентификатора *приложения клиента* (например, `33333333-3333-3333-3333-333333333333`ID клиента).
* Запись регистрации и входе в пользовательское имя, созданное `B2C_1_signupsignin`для приложения (например, ).

### <a name="create-the-app"></a>Создайте приложение

Замените заполнителей в следующей команде информацией, записанной ранее, и выполните команду в командном корпусе:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`). Имя папки также становится частью названия проекта.

> [!NOTE]
> Передайте опцион App `app-id-uri` ID URI, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, описанное в разделе [прицелов доступа.](#access-token-scopes)

## <a name="server-app-configuration"></a>Конфигурация приложения сервера

*Этот раздел относится к приложению **Server** решения.*

### <a name="authentication-package"></a>Пакет аутентификации

Поддержка аутентификации и авторизации вызовов ASP.NET базовых `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`Web AIS обеспечивается:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Поддержка службы аутентификации

Метод `AddAuthentication` настраивает службы аутентификации в приложении и настраивает обработчик JWT Bearer как метод аутентификации по умолчанию. Метод `AddAzureADB2CBearer` устанавливает определенные параметры в обработчике JWT Bearer, необходимые для проверки токенов, испускаемых B2C Active Directory:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`и `UseAuthorization` обеспечить, чтобы:

* Приложение пытается разобрать и проверить токены на входящих запросах.
* Любой запрос, пытающихся получить доступ к защищенного ресурсу без надлежащих учетных данных, завершается неудачей.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

По умолчанию, `User.Identity.Name` не заселен.

Чтобы настроить приложение для получения значения `name` из типа претензии, назначь [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Параметры приложения

Файл *appsettings.json* содержит параметры настройки обработчика носителя JWT, используемого для проверки токенов доступа.

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Контроллер WeatherForecast

Контроллер WeatherForecast *(Контроллеры/WeatherForecastController.cs*) предоставляет защищенный `[Authorize]` API с атрибутом, применяемым к контроллеру. **Важно** понимать, что:

* Атрибут `[Authorize]` в этом контроллере API является единственной вещью, которая защищает этот API от несанкционированного доступа.
* Атрибут, `[Authorize]` используемый Blazor в приложении WebAssembly, служит только намеком на приложение о том, что пользователь должен быть авторизован для правильной работы приложения.

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

*Этот раздел относится к **клиенту** приложения решения.*

### <a name="authentication-package"></a>Пакет аутентификации

Когда приложение создается для использования индивидуальной`IndividualB2C`учетной записи B2C ( ), приложение`Microsoft.Authentication.WebAssembly.Msal`автоматически получает ссылку на пакет для [библиотеки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( ). Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.

При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.

Пакет `Microsoft.Authentication.WebAssembly.Msal` транзитно добавляет `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.

### <a name="authentication-service-support"></a>Поддержка службы аутентификации

Поддержка аутентификации пользователей регистрируется `AddMsalAuthentication` в сервисном `Microsoft.Authentication.WebAssembly.Msal` контейнере с методом расширения, предусмотренным пакетом. Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком идентификационных данных (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Метод `AddMsalAuthentication` принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации Azure Portal AAD при регистрации приложения.

### <a name="access-token-scopes"></a>Области маркеров доступа

Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:

* Включен по умолчанию в знак в запросе.
* Используется для предоставления токена доступа сразу после проверки подлинности.

Все области должны принадлежать к одному и тому же приложению в правилах Active Directory Azure. Дополнительные области могут быть добавлены для дополнительных приложений API по мере необходимости:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Если портал Azure предоставляет область URI и **приложение выбрасывает необработанное исключение,** когда получает *401 несанкционированный* ответ от API, попробуйте использовать область URI, которая не включает схему и узел. Например, портал Azure может предоставить один из следующих форматов URI:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Поставка области URI без схемы и хозяина:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>ПеренаправлениеКомпонентToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Компонент аутентификации

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запустите приложение

Выполнить приложение из проекта Server. При использовании Visual Studio выберите проект Server в **Solution Explorer** и выберите кнопку **Run** в панели инструментов или запустите приложение из меню **Debug.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Запрос дополнительных токенов доступа](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Руководство по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
