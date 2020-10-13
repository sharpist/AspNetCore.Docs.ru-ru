---
title: Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core
author: guardrex
description: Узнайте, как настроить Blazor Server для сценариев обеспечения дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
ms.openlocfilehash: 89288f3fce2dbb6f2647693ba8aaf29500b5bb2b
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805496"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="dda38-103">Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dda38-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="dda38-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="dda38-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="dda38-105">Передача маркеров в приложение Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dda38-105">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="dda38-106">Маркеры, доступные за пределами компонентов Razor в приложении Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье.</span><span class="sxs-lookup"><span data-stu-id="dda38-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="dda38-107">Проверка подлинности приложения Blazor Server выполняется так же, как и для обычного приложения Razor Pages или MVC.</span><span class="sxs-lookup"><span data-stu-id="dda38-107">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="dda38-108">Подготовьте и сохраните маркеры в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dda38-108">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="dda38-109">Пример:</span><span class="sxs-lookup"><span data-stu-id="dda38-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="dda38-110">При необходимости дополнительные области добавляются с помощью `options.Scope.Add("{SCOPE}");`, где заполнитель `{SCOPE}` — это добавляемая дополнительная область.</span><span class="sxs-lookup"><span data-stu-id="dda38-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="dda38-111">Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dda38-111">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dda38-112">В `Startup.ConfigureServices` добавьте службы для следующего:</span><span class="sxs-lookup"><span data-stu-id="dda38-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="dda38-113">Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:</span><span class="sxs-lookup"><span data-stu-id="dda38-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dda38-114">В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:</span><span class="sxs-lookup"><span data-stu-id="dda38-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="dda38-115">В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:</span><span class="sxs-lookup"><span data-stu-id="dda38-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="dda38-116">Добавьте в приложение ссылку на пакет NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).</span><span class="sxs-lookup"><span data-stu-id="dda38-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="dda38-117">В службе, которая выполняет запрос безопасного API, внедрите поставщик токена и получите токен для запроса API.</span><span class="sxs-lookup"><span data-stu-id="dda38-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="dda38-118">Настройка схемы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dda38-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="dda38-119">Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="dda38-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="dda38-120">В следующем примере задается схема Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="dda38-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="dda38-121">Передача маркеров в приложение Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dda38-121">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="dda38-122">Маркеры, доступные за пределами компонентов Razor в приложении Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье.</span><span class="sxs-lookup"><span data-stu-id="dda38-122">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="dda38-123">Проверка подлинности приложения Blazor Server выполняется так же, как и для обычного приложения Razor Pages или MVC.</span><span class="sxs-lookup"><span data-stu-id="dda38-123">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="dda38-124">Подготовьте и сохраните маркеры в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dda38-124">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="dda38-125">Пример:</span><span class="sxs-lookup"><span data-stu-id="dda38-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="dda38-126">При необходимости дополнительные области добавляются с помощью `options.Scope.Add("{SCOPE}");`, где заполнитель `{SCOPE}` — это добавляемая дополнительная область.</span><span class="sxs-lookup"><span data-stu-id="dda38-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="dda38-127">При необходимости можно указать ресурс с помощью `options.Resource = "{RESOURCE}";`, где заполнитель `{RESOURCE}` — это ресурс.</span><span class="sxs-lookup"><span data-stu-id="dda38-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="dda38-128">Пример:</span><span class="sxs-lookup"><span data-stu-id="dda38-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="dda38-129">Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:</span><span class="sxs-lookup"><span data-stu-id="dda38-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dda38-130">Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dda38-130">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dda38-131">В `Startup.ConfigureServices` добавьте службы для следующего:</span><span class="sxs-lookup"><span data-stu-id="dda38-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="dda38-132">В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:</span><span class="sxs-lookup"><span data-stu-id="dda38-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="dda38-133">В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:</span><span class="sxs-lookup"><span data-stu-id="dda38-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="dda38-134">Добавьте в приложение ссылку на пакет NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).</span><span class="sxs-lookup"><span data-stu-id="dda38-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="dda38-135">В службе, которая выполняет запрос безопасного API, внедрите поставщик токена и получите токен для запроса API.</span><span class="sxs-lookup"><span data-stu-id="dda38-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="dda38-136">Настройка схемы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dda38-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="dda38-137">Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="dda38-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="dda38-138">В следующем примере задается схема Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="dda38-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="dda38-139">Использование конечных точек OpenID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="dda38-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="dda38-140">До версии ASP.NET Core 5.0 в библиотеке проверки подлинности и шаблонах Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="dda38-140">In versions of ASP.NET Core prior to 5.0, the authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="dda38-141">Чтобы использовать конечную точку версии 2.0 с версиями ASP.NET Core до 5.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>.</span><span class="sxs-lookup"><span data-stu-id="dda38-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="dda38-142">Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="dda38-142">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="dda38-143">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую.</span><span class="sxs-lookup"><span data-stu-id="dda38-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="dda38-144">Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, либо в файле параметров приложения с помощью ключа <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.</span><span class="sxs-lookup"><span data-stu-id="dda38-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="dda38-145">Изменения кода</span><span class="sxs-lookup"><span data-stu-id="dda38-145">Code changes</span></span>

* <span data-ttu-id="dda38-146">Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="dda38-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="dda38-147">Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="dda38-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="dda38-148">документации по Azure.</span><span class="sxs-lookup"><span data-stu-id="dda38-148">in the Azure documentation.</span></span>
* <span data-ttu-id="dda38-149">Поскольку ресурсы указываются в URI области для конечных точек версии 2.0, удалите параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>.</span><span class="sxs-lookup"><span data-stu-id="dda38-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="dda38-150">Дополнительные сведения см. в разделе [Области, а не ресурсы](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) в документации Azure.</span><span class="sxs-lookup"><span data-stu-id="dda38-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="dda38-151">Универсальный код ресурса идентификатора приложения</span><span class="sxs-lookup"><span data-stu-id="dda38-151">App ID URI</span></span>

* <span data-ttu-id="dda38-152">При использовании конечных точек версии 2.0 в интерфейсах API определяется код *`App ID URI`* , который должен представлять уникальный идентификатор интерфейса API.</span><span class="sxs-lookup"><span data-stu-id="dda38-152">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="dda38-153">Универсальный код ресурса (URI) идентификатора приложения включается во все области в качестве префикса, а конечные точки версии 2.0 выдают маркеры доступа с кодом URI идентификатора приложения в качестве аудитории.</span><span class="sxs-lookup"><span data-stu-id="dda38-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="dda38-154">При использовании конечных точек версии 2.0 идентификатор клиента, настроенный в API сервера, изменяется с идентификатора приложения API (идентификатора клиента) на код URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="dda38-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="dda38-155">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="dda38-155">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="dda38-156">URI идентификатора приложения для использования находится в описании регистрации приложения поставщика OIDC.</span><span class="sxs-lookup"><span data-stu-id="dda38-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
