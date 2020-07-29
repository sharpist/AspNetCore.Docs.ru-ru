---
title: Сценарии обеспечения дополнительной безопасности [Blazor Server для ASP.NET Core
author: guardrex
description: Узнайте, как настроить [Blazor Server для сценариев обеспечения дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 46de9a22dec540b8dfda7583b7a3c5c2dcbbc549
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402329"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Сценарии обеспечения дополнительной безопасности [Blazor Server для ASP.NET Core

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Передача маркеров в приложение [Blazor Server

Маркеры, доступные за пределами компонентов [Razor в приложении [Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье. Пример кода, в том числе полный пример `Startup.ConfigureServices`, см. в разделе [Передача маркеров в серверные приложения [Blazor](https://github.com/javiercn/blazor-server-aad-sample).

Проверка подлинности приложения [Blazor Server выполняется так же, как и для обычного приложения [Razor Pages или MVC. Подготовьте и сохраните маркеры в файле cookie проверки подлинности. Пример:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении [Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В `Startup.ConfigureServices` добавьте службы для следующего:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

В службе, которая выполняет запрос безопасного API, вставьте поставщик маркера и получите маркер для вызова API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a>Настройка схемы проверки подлинности

Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует [Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`. В следующем примере задается схема Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Использование конечных точек Open ID Connect (OIDC) версии 2.0

В библиотеке проверки подлинности и шаблонах [Blazor используются конечные точки Open ID Connect (OIDC) версии 1.0. Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую. Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, либо в файле параметров приложения с помощью ключа <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.

### <a name="code-changes"></a>Изменения кода

* Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0. Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) документации по Azure.
* Поскольку ресурсы указываются в URI области для конечных точек версии 2.0, удалите параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

URI идентификатора приложения для использования находится в описании регистрации приложения поставщика OIDC.
