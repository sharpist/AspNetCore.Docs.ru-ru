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
ms.openlocfilehash: f172df7f2333fd79ba175ec8a3a6925ec07f1113
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851529"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="52d2d-103">BlazorДополнительные сценарии безопасности ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="52d2d-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="52d2d-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="52d2d-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="52d2d-105">Передача маркеров в Blazor серверное приложение</span><span class="sxs-lookup"><span data-stu-id="52d2d-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="52d2d-106">Токены, доступные за пределами Razor компонентов Blazor серверного приложения, можно передавать в компоненты с помощью подхода, описанного в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="52d2d-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="52d2d-107">Пример кода, включая полный `Startup.ConfigureServices` пример, см. в разделе [Передача токенов в Blazor приложение на стороне сервера](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="52d2d-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="52d2d-108">Проверяйте подлинность Blazor серверного приложения так же, как обычные Razor страницы или приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="52d2d-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="52d2d-109">Подготавливает и сохраняйте маркеры в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="52d2d-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="52d2d-110">Пример.</span><span class="sxs-lookup"><span data-stu-id="52d2d-110">For example:</span></span>

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

<span data-ttu-id="52d2d-111">Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:</span><span class="sxs-lookup"><span data-stu-id="52d2d-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="52d2d-112">Определите службу поставщика маркеров с заданной **областью** , которую можно использовать в Blazor приложении для разрешения маркеров [путем внедрения зависимостей (DI)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="52d2d-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="52d2d-113">В `Startup.ConfigureServices` службах добавьте службы для:</span><span class="sxs-lookup"><span data-stu-id="52d2d-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="52d2d-114">В файле *_Host. cshtml* создайте экземпляр и `InitialApplicationState` передайте его в качестве параметра в приложение:</span><span class="sxs-lookup"><span data-stu-id="52d2d-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="52d2d-115">В `App` компоненте (*app. Razor*) разрешите службу и инициализируйте ее с помощью данных из параметра:</span><span class="sxs-lookup"><span data-stu-id="52d2d-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="52d2d-116">В службе, которая выполняет запрос безопасного API, вставьте поставщик маркера и получите маркер для вызова API:</span><span class="sxs-lookup"><span data-stu-id="52d2d-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="52d2d-117">Использование конечных точек Open ID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="52d2d-117">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="52d2d-118">Библиотека проверки подлинности и Blazor Шаблоны используют конечные точки Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="52d2d-118">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="52d2d-119">Чтобы использовать конечную точку версии 2.0, настройте <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> параметр в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="52d2d-119">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="52d2d-120">Кроме того, параметр можно задать в файле параметров приложения (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="52d2d-120">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="52d2d-121">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например с поставщиками, не являющимися владельцами AAD, задайте <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> свойство напрямую.</span><span class="sxs-lookup"><span data-stu-id="52d2d-121">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="52d2d-122">Либо установите свойство в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> или в файле параметров приложения с помощью <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> ключа.</span><span class="sxs-lookup"><span data-stu-id="52d2d-122">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="52d2d-123">Изменения в коде</span><span class="sxs-lookup"><span data-stu-id="52d2d-123">Code changes</span></span>

* <span data-ttu-id="52d2d-124">Список утверждений в токене идентификатора изменяется для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="52d2d-124">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="52d2d-125">Дополнительные сведения см [. в статье Зачем обновлять платформу Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="52d2d-125">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="52d2d-126">в документации Azure.</span><span class="sxs-lookup"><span data-stu-id="52d2d-126">in the Azure documentation.</span></span>
* <span data-ttu-id="52d2d-127">Так как ресурсы указываются в URI области для конечных точек версии 2.0, удалите <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> параметр свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="52d2d-127">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

<span data-ttu-id="52d2d-128">Вы можете найти URI идентификатора приложения для использования в описании регистрации приложения поставщика OIDC.</span><span class="sxs-lookup"><span data-stu-id="52d2d-128">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
