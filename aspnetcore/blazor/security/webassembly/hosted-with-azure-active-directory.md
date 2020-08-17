---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 08e202f0876d01a98c099424fd65b06cbe82a3b2
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130344"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

В этой статье описывается, как создать [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.

## <a name="register-apps-in-aad-and-create-solution"></a>Регистрация приложений в AAD и создание решения

### <a name="create-a-tenant"></a>Создание клиента

Следуйте инструкциям в разделе [Краткое руководство. Настройка клиента](/azure/active-directory/develop/quickstart-create-new-tenant), чтобы создать клиент в AAD.

### <a name="register-a-server-api-app"></a>Регистрация приложения API сервера

Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для *приложения API сервера*, а затем выполните следующие действия.

1. В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, **Blazor Server AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).
1. В этом сценарии *приложению API сервера* не требуется **URI перенаправления**, поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите следующие сведения:

* Идентификатор *приложения API сервера* (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).
* Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).
* Основной домен AAD/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`). домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.

В разделе **Разрешения API** удалите разрешение **Microsoft Graph** > **User.Read**, поскольку приложению не требуется вход или доступ к профилю пользователя.

В разделе **Предоставление API**:

1. Нажмите **Добавить группу**.
1. Выберите **Сохранить и продолжить**.
1. Укажите значение в поле **Имя области** (например, `API.Access`).
1. Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).
1. Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).
1. Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.
1. Выберите **Добавить область**.

Запишите следующие сведения:

* URI идентификатора приложения (например, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` или настраиваемое значение)
* Область по умолчанию (например, `API.Access`)

URI идентификатора приложения может требовать специальную конфигурацию в клиентском приложении, которая описана в разделе [Области маркеров доступа](#access-token-scopes) этой статьи.

### <a name="register-a-client-app"></a>Регистрация клиентского приложения

Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для *клиентского приложения*, а затем выполните следующие действия.

1. В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.
1. Укажите **имя** приложения (например, **BlazorКлиентский AAD**).
1. Выберите **поддерживаемые типы учетных записей**. Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).
1. В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`. Порт по умолчанию для приложения, работающего на Kestrel, — 5001. Если приложение выполняется на другом порте Kestrel, используйте порт приложения. Для IIS Express созданный случайным образом порт для приложения указан в свойствах серверного приложения на панели **Отладка**. Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления. В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.
1. Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.
1. Выберите **Зарегистрировать**.

Запишите идентификатор *клиентского приложения* (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).

В разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** выполните следующие действия.

1. Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.
1. В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.
1. Для остальных параметров можно оставить значения по умолчанию.
1. Нажмите кнопку **Сохранить**.

В разделе **Разрешения API** выполните следующие действия:

1. Убедитесь, что у приложения имеется разрешение **Microsoft Graph** > **User.Read**.
1. Выберите **Добавить разрешение**, а затем — **Мои API**.
1. В столбце *Имя* выберите **Приложение API сервера** (например, **Blazor Server AAD**).
1. Откройте список **API**.
1. Разрешите доступ к API (например, `API.Access`).
1. Выберите **Добавить разрешения**.
1. Нажмите кнопку **Предоставить согласие администратора для {ИМЯ КЛИЕНТА}** . Выберите **Да** для подтверждения.

### <a name="create-the-app"></a>Создание приложения

В пустой папке замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Заполнитель                  | Название на портале Azure                                     | Пример                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | Идентификатор приложения (клиента) для *клиентского приложения*          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | Имя области                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | Идентификатор приложения (клиента) для *приложения API сервера*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | URI идентификатора приложения ([см. примечание](#access-token-scopes)) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | Основной домен/домен издателя/домен клиента                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | Идентификатор каталога (клиента)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.

> [!NOTE]
> Передайте URI идентификатора приложения в параметр `app-id-uri`, но обратите внимание, что в клиентском приложении может потребоваться изменить конфигурацию, как описано в разделе [Области маркеров доступа](#access-token-scopes).

> [!NOTE]
> На портале Azure в разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** > **URI перенаправления** в качестве порта для приложений, выполняющихся на сервере Kestrel с параметрами по умолчанию, для *клиентского приложения* задан порт 5001.
>
> Если *клиентское приложение* выполняется на случайном порту IIS Express, порт для приложения указан в свойствах *приложения API сервера* на панели **Отладка**.
>
> Если порт не был настроен ранее с помощью известного порта *клиентского приложения*, вернитесь к регистрации *клиентского приложения* на портале Azure и обновите URI перенаправления, указав правильный порт.

## <a name="server-app-configuration"></a>Конфигурация серверного приложения

*Этот раздел относится к приложению **`Server`** решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/).

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию. Метод <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:

* Приложение пытается проанализировать и проверить маркеры в входящих запросах.
* Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>User.Identity.Name

По умолчанию API серверного приложения заполняет `User.Identity.Name` значением из типа утверждения `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

Чтобы настроить приложение на получение значения из типа утверждения `name`, настройте <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Параметры приложения

В файле `appsettings.json` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>Контроллер WeatherForecast

Контроллер WeatherForecast (*Controllers/WeatherForecastController.cs*) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру. **Важно** понять следующее:

* Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.
* Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении Blazor WebAssembly, служит подсказкой для приложения о том, что пользователь должен пройти авторизацию, чтобы приложение работало правильно.

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

*Этот раздел относится к приложению **`Client`** решения.*

### <a name="authentication-package"></a>Пакет проверки подлинности

Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)). В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в приложение.

### <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.

`Program.cs`.

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.ServerAPI`).

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/). Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).

`Program.cs`.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.

Конфигурация предоставлена в файле `wwwroot/appsettings.json`:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Области маркеров доступа

Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:

* включены по умолчанию в запросе на вход;
* используются для предоставления маркера доступа сразу после проверки подлинности.

Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory. При необходимости можно добавить дополнительные области для дополнительных приложений API:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:

* [Запрашивание дополнительных маркеров доступа](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Присоединение маркеров к исходящим запросам](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Страница индексов

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Компонент RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Компонент LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Компонент FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение из серверного проекта. При использовании Visual Studio выполните одно из следующих действий.

* На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.
* В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/additional-scenarios>
* [Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
