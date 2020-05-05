---
title: Дополнительные Blazor сценарии безопасности ASP.NET Core Server
author: guardrex
description: Узнайте, как настроить Blazor сервер для дополнительных сценариев безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772808"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Дополнительные Blazor сценарии безопасности ASP.NET Core Server

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Передача маркеров в Blazor серверное приложение

Токены, доступные за Razor пределами Blazor компонентов серверного приложения, можно передавать в компоненты с помощью подхода, описанного в этом разделе. Пример кода, включая полный `Startup.ConfigureServices` пример, см. в разделе [Передача токенов в приложение на стороне Blazor сервера](https://github.com/javiercn/blazor-server-aad-sample).

Проверяйте Blazor подлинность серверного приложения так же Razor , как обычные страницы или приложения MVC. Подготавливает и сохраняйте маркеры в файле cookie проверки подлинности. Например:

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

Определите службу поставщика маркеров с заданной **областью** , которую можно использовать Blazor в приложении для разрешения маркеров [путем внедрения зависимостей (DI)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В `Startup.ConfigureServices`службах добавьте службы для:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

В файле *_Host. cshtml* создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:

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
