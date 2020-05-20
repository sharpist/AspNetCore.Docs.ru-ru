---
title: BlazorДополнительные сценарии безопасности ASP.NET Core Server
author: guardrex
description: Узнайте, как настроить Blazor сервер для дополнительных сценариев безопасности.
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
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 9d26cde4d8964a8285241bb0158d8e6f8d5f8dbc
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444078"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>BlazorДополнительные сценарии безопасности ASP.NET Core Server

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Передача маркеров в Blazor серверное приложение

Токены, доступные за пределами Razor компонентов Blazor серверного приложения, можно передавать в компоненты с помощью подхода, описанного в этом разделе. Пример кода, включая полный `Startup.ConfigureServices` пример, см. в разделе [Передача токенов в Blazor приложение на стороне сервера](https://github.com/javiercn/blazor-server-aad-sample).

Проверяйте подлинность Blazor серверного приложения так же, как обычные Razor страницы или приложения MVC. Подготавливает и сохраняйте маркеры в файле cookie проверки подлинности. Пример:

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

Определите службу поставщика маркеров с заданной **областью** , которую можно использовать в Blazor приложении для разрешения маркеров [путем внедрения зависимостей (DI)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В `Startup.ConfigureServices` службах добавьте службы для:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

В файле *_Host. cshtml* создайте экземпляр и `InitialApplicationState` передайте его в качестве параметра в приложение:

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

В `App` компоненте (*app. Razor*) разрешите службу и инициализируйте ее с помощью данных из параметра:

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Использование конечных точек Open ID Connect (OIDC) версии 2.0

Библиотека проверки подлинности и Blazor Шаблоны используют конечные точки Open ID Connect (OIDC) v 1.0. Чтобы использовать конечную точку версии 2.0, настройте <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> параметр в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Кроме того, параметр можно задать в файле параметров приложения (*appSettings. JSON*):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например с поставщиками, не являющимися владельцами AAD, задайте `Authority` свойство напрямую. Либо установите свойство в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> или в файле параметров приложения с помощью `Authority` ключа.

### <a name="code-changes"></a>Изменения в коде

* Список утверждений в токене идентификатора изменяется для конечных точек версии 2.0. Дополнительные сведения см [. в статье Зачем обновлять платформу Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) в документации Azure.
* Так как ресурсы указываются в URI области для конечных точек версии 2.0, удалите <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> параметр свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

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

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Вы можете найти URI идентификатора приложения для использования в описании регистрации приложения поставщика OIDC.
