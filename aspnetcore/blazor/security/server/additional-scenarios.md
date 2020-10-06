---
title: Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core
author: guardrex
description: Узнайте, как настроить Blazor Server для сценариев обеспечения дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393669"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="d8529-103">Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8529-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="d8529-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="d8529-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="d8529-105">Передача маркеров в приложение Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d8529-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="d8529-106">Маркеры, доступные за пределами компонентов Razor в приложении Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье.</span><span class="sxs-lookup"><span data-stu-id="d8529-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="d8529-107">Пример кода, в том числе полный пример `Startup.ConfigureServices`, см. в разделе [Передача маркеров в серверные приложения Blazor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="d8529-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="d8529-108">Проверка подлинности приложения Blazor Server выполняется так же, как и для обычного приложения Razor Pages или MVC.</span><span class="sxs-lookup"><span data-stu-id="d8529-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="d8529-109">Подготовьте и сохраните маркеры в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="d8529-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="d8529-110">Пример:</span><span class="sxs-lookup"><span data-stu-id="d8529-110">For example:</span></span>

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

<span data-ttu-id="d8529-111">Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:</span><span class="sxs-lookup"><span data-stu-id="d8529-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d8529-112">Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d8529-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d8529-113">В `Startup.ConfigureServices` добавьте службы для следующего:</span><span class="sxs-lookup"><span data-stu-id="d8529-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="d8529-114">В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:</span><span class="sxs-lookup"><span data-stu-id="d8529-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="d8529-115">В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:</span><span class="sxs-lookup"><span data-stu-id="d8529-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="d8529-116">Добавьте в приложение ссылку на пакет NuGet [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).</span><span class="sxs-lookup"><span data-stu-id="d8529-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="d8529-117">В службе, которая выполняет запрос безопасного API, вставьте поставщик маркера и получите маркер для вызова API:</span><span class="sxs-lookup"><span data-stu-id="d8529-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="d8529-118">Настройка схемы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="d8529-118">Set the authentication scheme</span></span>

<span data-ttu-id="d8529-119">Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d8529-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="d8529-120">В следующем примере задается схема Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="d8529-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="d8529-121">Использование конечных точек OpenID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="d8529-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="d8529-122">В библиотеке проверки подлинности и шаблонах Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="d8529-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="d8529-123">Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="d8529-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="d8529-124">Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="d8529-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="d8529-125">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую.</span><span class="sxs-lookup"><span data-stu-id="d8529-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="d8529-126">Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, либо в файле параметров приложения с помощью ключа <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.</span><span class="sxs-lookup"><span data-stu-id="d8529-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="d8529-127">Изменения кода</span><span class="sxs-lookup"><span data-stu-id="d8529-127">Code changes</span></span>

* <span data-ttu-id="d8529-128">Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="d8529-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="d8529-129">Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="d8529-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="d8529-130">документации по Azure.</span><span class="sxs-lookup"><span data-stu-id="d8529-130">in the Azure documentation.</span></span>
* <span data-ttu-id="d8529-131">Поскольку ресурсы указываются в URI области для конечных точек версии 2.0, удалите параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>.</span><span class="sxs-lookup"><span data-stu-id="d8529-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="d8529-132">Дополнительные сведения см. в разделе [Области, а не ресурсы](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) в документации Azure.</span><span class="sxs-lookup"><span data-stu-id="d8529-132">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="d8529-133">Универсальный код ресурса идентификатора приложения</span><span class="sxs-lookup"><span data-stu-id="d8529-133">App ID URI</span></span>

* <span data-ttu-id="d8529-134">При использовании конечных точек версии 2.0 в интерфейсах API определяется код *`App ID URI`* , который должен представлять уникальный идентификатор интерфейса API.</span><span class="sxs-lookup"><span data-stu-id="d8529-134">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="d8529-135">Универсальный код ресурса (URI) идентификатора приложения включается во все области в качестве префикса, а конечные точки версии 2.0 выдают маркеры доступа с кодом URI идентификатора приложения в качестве аудитории.</span><span class="sxs-lookup"><span data-stu-id="d8529-135">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="d8529-136">При использовании конечных точек версии 2.0 идентификатор клиента, настроенный в API сервера, изменяется с идентификатора приложения API (идентификатора клиента) на код URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="d8529-136">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="d8529-137">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="d8529-137">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="d8529-138">URI идентификатора приложения для использования находится в описании регистрации приложения поставщика OIDC.</span><span class="sxs-lookup"><span data-stu-id="d8529-138">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
