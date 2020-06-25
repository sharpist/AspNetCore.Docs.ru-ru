---
title: Сценарии обеспечения дополнительной безопасности ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для сценариев дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 2020b422ad48a9c4c52f2670fd3b5054aa4d60c5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103516"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ae803-103">Сценарии обеспечения дополнительной безопасности ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ae803-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ae803-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="ae803-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ae803-105">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="ae803-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ae803-106">Службу <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно использовать с <xref:System.Net.Http.HttpClient> для присоединения маркеров доступа к исходящим запросам.</span><span class="sxs-lookup"><span data-stu-id="ae803-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ae803-107">Маркеры получаются с помощью существующей службы <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>.</span><span class="sxs-lookup"><span data-stu-id="ae803-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="ae803-108">Если маркер получить невозможно, создается исключение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="ae803-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="ae803-109">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> есть <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, с помощью которого можно направить пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="ae803-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ae803-110">Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно настроить разрешенные URL-адреса, области и URL-адреса возврата с помощью метода <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="ae803-111">Чтобы настроить обработчик сообщений для исходящих запросов, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="ae803-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="ae803-112">[Пользовательский класс AuthorizationMessageHandler](#custom-authorizationmessagehandler-class) (*рекомендуется*)</span><span class="sxs-lookup"><span data-stu-id="ae803-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="ae803-113">Configure AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ae803-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="ae803-114">Пользовательский класс AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ae803-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="ae803-115">В следующем примере класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> расширяется пользовательским классом, который можно использовать для настройки <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="ae803-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="ae803-116">В `Program.Main` (*Program.cs*) объект <xref:System.Net.Http.HttpClient> настраивается с помощью пользовательского обработчика сообщений авторизации:</span><span class="sxs-lookup"><span data-stu-id="ae803-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="ae803-117">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [try-catch](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="ae803-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="ae803-118">Когда клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (из пакета [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)), при выполнении запросов к интерфейсу API сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа:</span><span class="sxs-lookup"><span data-stu-id="ae803-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="ae803-119">Configure AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ae803-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="ae803-120">В следующем примере обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивает объект <xref:System.Net.Http.HttpClient> в `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="ae803-121">Для удобства включен обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> с базовым адресом приложения, предварительно настроенным в качестве авторизованного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="ae803-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ae803-122">В шаблонах Blazor WebAssembly с поддержкой проверки подлинности теперь используется интерфейс <xref:System.Net.Http.IHttpClientFactory> (из пакета [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)) в проекте API сервера для настройки объекта <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span><span class="sxs-lookup"><span data-stu-id="ae803-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="ae803-123">Когда в предыдущем примере клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>, при выполнении запросов к проекту сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа.</span><span class="sxs-lookup"><span data-stu-id="ae803-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ae803-124">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [try-catch](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="ae803-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="ae803-125">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="ae803-125">Typed HttpClient</span></span>

<span data-ttu-id="ae803-126">Можно определить типизированный клиент, который будет обрабатывать все аспекты, связанные с HTTP и получением маркеров, в пределах одного класса.</span><span class="sxs-lookup"><span data-stu-id="ae803-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ae803-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae803-127">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="ae803-128">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="ae803-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="ae803-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ae803-130">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="ae803-130">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ae803-131">Настройка обработчика HttpClient</span><span class="sxs-lookup"><span data-stu-id="ae803-131">Configure the HttpClient handler</span></span>

<span data-ttu-id="ae803-132">Обработчик можно дополнительно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="ae803-132">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ae803-133">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ae803-134">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="ae803-134">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ae803-135">Если приложение Blazor WebAssembly обычно использует защищенный клиент <xref:System.Net.Http.HttpClient> по умолчанию, приложение также может выполнять неавторизованные или не прошедшие проверку запросы к веб-API путем настройки именованного клиента <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="ae803-135">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ae803-136">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-136">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ae803-137">Приведенная выше регистрация является дополнением к существующей регистрации защищенного клиента <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ae803-137">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ae803-138">Компонент создает клиент <xref:System.Net.Http.HttpClient> на основе <xref:System.Net.Http.IHttpClientFactory> (из пакета [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)) для выполнения не прошедших проверку или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="ae803-138">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="ae803-139">Контроллер в интерфейсе API сервера (`WeatherForecastNoAuthenticationController` в предыдущем примере) не помечен атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="ae803-139">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ae803-140">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="ae803-140">Request additional access tokens</span></span>

<span data-ttu-id="ae803-141">Маркеры доступа можно получать вручную, вызывая `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="ae803-141">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="ae803-142">В приведенном ниже примере для чтения данных пользователей и отправки почты приложениям требуются дополнительные области API Microsoft Graph Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="ae803-142">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ae803-143">После добавления разрешений API Microsoft Graph на портале Azure AAD дополнительные области настраиваются в клиентском приложении.</span><span class="sxs-lookup"><span data-stu-id="ae803-143">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="ae803-144">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-144">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="ae803-145">Метод `IAccessTokenProvider.RequestToken` предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с указанным набором областей.</span><span class="sxs-lookup"><span data-stu-id="ae803-145">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ae803-146">В компоненте Razor:</span><span class="sxs-lookup"><span data-stu-id="ae803-146">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="ae803-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> возвращает:</span><span class="sxs-lookup"><span data-stu-id="ae803-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="ae803-148">`true` с пригодным для использования `token`.</span><span class="sxs-lookup"><span data-stu-id="ae803-148">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ae803-149">`false`, если маркер не получен.</span><span class="sxs-lookup"><span data-stu-id="ae803-149">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ae803-150">HttpClient и HttpRequestMessage с параметрами запроса Fetch API</span><span class="sxs-lookup"><span data-stu-id="ae803-150">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ae803-151">При выполнении в WebAssembly в приложении Blazor WebAssembly для настройки запросов можно использовать [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="ae803-151">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="ae803-152">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="ae803-152">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="ae803-153">Следующий компонент выполняет запрос `POST` к конечной точке API списка дел на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="ae803-153">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="ae803-154">Реализация .NET WebAssembly <xref:System.Net.Http.HttpClient> использует [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="ae803-154">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="ae803-155">Извлечение позволяет настроить несколько [параметров, связанных с запросами](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ae803-155">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="ae803-156">Параметры запроса HTTP на извлечение можно настроить с помощью методов расширения <xref:System.Net.Http.HttpRequestMessage>, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="ae803-156">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="ae803-157">Метод расширения</span><span class="sxs-lookup"><span data-stu-id="ae803-157">Extension method</span></span> | <span data-ttu-id="ae803-158">Свойство запроса на извлечение</span><span class="sxs-lookup"><span data-stu-id="ae803-158">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="ae803-159">credentials</span><span class="sxs-lookup"><span data-stu-id="ae803-159">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="ae803-160">cache</span><span class="sxs-lookup"><span data-stu-id="ae803-160">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="ae803-161">mode</span><span class="sxs-lookup"><span data-stu-id="ae803-161">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="ae803-162">integrity</span><span class="sxs-lookup"><span data-stu-id="ae803-162">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="ae803-163">Вы можете задать дополнительные параметры с помощью более универсального метода расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-163">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="ae803-164">HTTP-ответ обычно помещается в буфер в приложении Blazor WebAssembly для выполнения операций синхронизации содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="ae803-164">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="ae803-165">Для выполнения потоковой передачи ответов используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> для запроса.</span><span class="sxs-lookup"><span data-stu-id="ae803-165">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="ae803-166">Чтобы включить учетные данные в запрос независимо от источника, используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-166">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="ae803-167">Дополнительные сведения о возможностях Fetch API см. в разделе [Веб-документы MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ae803-167">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ae803-168">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS заголовок `Authorization` должен быть разрешен политикой CORS.</span><span class="sxs-lookup"><span data-stu-id="ae803-168">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ae803-169">Следующая политика включает в себя настройку для следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="ae803-169">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ae803-170">Источники запроса (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ae803-170">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ae803-171">Любой метод (команда).</span><span class="sxs-lookup"><span data-stu-id="ae803-171">Any method (verb).</span></span>
* <span data-ttu-id="ae803-172">Заголовки `Content-Type` и `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="ae803-172">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ae803-173">Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при вызове <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="ae803-173">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ae803-174">Учетные данные, установленные клиентским кодом JavaScript (для свойства `credentials` задано значение `include`).</span><span class="sxs-lookup"><span data-stu-id="ae803-174">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ae803-175">Дополнительные сведения см. в разделе <xref:security/cors> и компоненте "Тестер HTTP-запросов" примера приложения (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="ae803-175">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ae803-176">Обработка ошибок при выполнении запросов маркеров</span><span class="sxs-lookup"><span data-stu-id="ae803-176">Handle token request errors</span></span>

<span data-ttu-id="ae803-177">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в приложении SPA и в поставщике Identity (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="ae803-177">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ae803-178">Маркеры, которые IP-адрес выдает для пользователя, обычно действительны недолго (как правило, около часа), поэтому клиентское приложение должно регулярно получать новые маркеры.</span><span class="sxs-lookup"><span data-stu-id="ae803-178">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ae803-179">В противном случае после истечения срока действия предоставленных маркеров будет выполнен выход пользователя из системы.</span><span class="sxs-lookup"><span data-stu-id="ae803-179">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ae803-180">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры без повторного прохождения проверки подлинности пользователем благодаря сохранению состояния проверки подлинности или сеанса в поставщике удостоверений.</span><span class="sxs-lookup"><span data-stu-id="ae803-180">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ae803-181">В некоторых случаях клиент не может получить маркер без участия пользователя, например, если по какой-либо причине пользователь явно вышел из поставщика удостоверений.</span><span class="sxs-lookup"><span data-stu-id="ae803-181">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ae803-182">Такая ситуация возникает, если пользователь переходит по адресу `https://login.microsoftonline.com` и выполняет выход из системы. В этих случаях приложению не становится известно о выходе пользователя из системы немедленно. Любой маркер, который имеется у клиента, может быть уже недействительным.</span><span class="sxs-lookup"><span data-stu-id="ae803-182">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ae803-183">Кроме того, клиент не может подготовить новый маркер без участия пользователя после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="ae803-183">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ae803-184">Такие сценарии нехарактерны для проверки подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="ae803-184">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ae803-185">Они проистекают из особенностей одностраничных приложений.</span><span class="sxs-lookup"><span data-stu-id="ae803-185">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ae803-186">Одностраничному приложению, использующему файлы cookie, также не удастся вызвать интерфейс API сервера в случае удаления файла cookie для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-186">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ae803-187">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="ae803-187">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ae803-188">Для подготовки нового маркера доступа с целью вызова API пользователю может потребоваться пройти проверку подлинности повторно.</span><span class="sxs-lookup"><span data-stu-id="ae803-188">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ae803-189">Даже если у клиента есть маркер, который представляется действительным, вызов сервера может завершиться ошибкой из-за того, что маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="ae803-189">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ae803-190">Когда приложение запрашивает маркер, возможны два результата:</span><span class="sxs-lookup"><span data-stu-id="ae803-190">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ae803-191">Запрос выполняется успешно, и приложение получает действительный маркер.</span><span class="sxs-lookup"><span data-stu-id="ae803-191">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ae803-192">Запрос завершается ошибкой, и приложение должно повторно провести проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="ae803-192">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ae803-193">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="ae803-193">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ae803-194">Ниже приведены возможные подходы в порядке увеличения сложности.</span><span class="sxs-lookup"><span data-stu-id="ae803-194">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ae803-195">Сохраните текущее состояние страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="ae803-195">Store the current page state in session storage.</span></span> <span data-ttu-id="ae803-196">Во время [события жизненного цикла OnInitializedAsync](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) проверьте, можно ли восстановить состояние, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="ae803-196">During the [OnInitializedAsync lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ae803-197">Добавьте параметр строки запроса и используйте его, чтобы сообщить приложению о необходимости восстановить ранее сохраненное состояние.</span><span class="sxs-lookup"><span data-stu-id="ae803-197">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ae803-198">Добавьте параметр строки запроса с уникальным идентификатором для сохранения данных в хранилище сеансов без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="ae803-198">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ae803-199">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="ae803-199">The following example shows how to:</span></span>

* <span data-ttu-id="ae803-200">сохранить состояние перед перенаправлением на страницу входа;</span><span class="sxs-lookup"><span data-stu-id="ae803-200">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ae803-201">восстановить предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="ae803-201">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ae803-202">Сохранение состояния приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ae803-202">Save app state before an authentication operation</span></span>

<span data-ttu-id="ae803-203">Во время операции проверки подлинности существуют случаи, когда необходимо сохранить состояние приложения, прежде чем в браузере будет выполнено перенаправление в поставщик удостоверений.</span><span class="sxs-lookup"><span data-stu-id="ae803-203">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ae803-204">Например, такая потребность может возникнуть, когда вы используете контейнер состояния и хотите восстановить состояние после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-204">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ae803-205">Вы можете использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстанавливать это состояние после успешного завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-205">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="ae803-206">Такой подход демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="ae803-206">The following example demonstrates the approach.</span></span>

<span data-ttu-id="ae803-207">В приложении создается класс контейнера состояния со свойствами для хранения значений состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="ae803-207">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="ae803-208">В приведенном ниже примере контейнер используется для хранения значения счетчика для компонента `Counter` шаблона по умолчанию (*Pages/Counter.razor*).</span><span class="sxs-lookup"><span data-stu-id="ae803-208">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="ae803-209">Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="ae803-209">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="ae803-210">Компонент `Counter` использует контейнер состояния для хранения значения `currentCount` за пределами компонента:</span><span class="sxs-lookup"><span data-stu-id="ae803-210">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="ae803-211">Создайте `ApplicationAuthenticationState` на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="ae803-211">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="ae803-212">Укажите свойство `Id`, которое служит идентификатором для локально хранящегося состояния.</span><span class="sxs-lookup"><span data-stu-id="ae803-212">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="ae803-213">*ApplicationAuthenticationState.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae803-213">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="ae803-214">Компонент `Authentication` (*Pages/Authentication.razor*) сохраняет и восстанавливает состояние приложения, используя локальное хранилище сеансов, с помощью методов сериализации и десериализации `StateContainer` (`GetStateForLocalStorage` и `SetStateFromLocalStorage`):</span><span class="sxs-lookup"><span data-stu-id="ae803-214">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="ae803-215">В этом примере для проверки подлинности используется Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="ae803-215">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="ae803-216">В `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-216">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="ae803-217">`ApplicationAuthenticationState` настраивается с типом `RemoteAuthenticationState` из библиотеки проверки подлинности Майкрософт (MSAL).</span><span class="sxs-lookup"><span data-stu-id="ae803-217">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="ae803-218">Контейнер состояния регистрируется в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="ae803-218">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="ae803-219">Настройка маршрутов приложения</span><span class="sxs-lookup"><span data-stu-id="ae803-219">Customize app routes</span></span>

<span data-ttu-id="ae803-220">Для представления различных состояний проверки подлинности библиотека [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) по умолчанию использует маршруты, приведенные в таблице ниже.</span><span class="sxs-lookup"><span data-stu-id="ae803-220">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ae803-221">Маршрут</span><span class="sxs-lookup"><span data-stu-id="ae803-221">Route</span></span>                            | <span data-ttu-id="ae803-222">Цель</span><span class="sxs-lookup"><span data-stu-id="ae803-222">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ae803-223">Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="ae803-223">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ae803-224">Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="ae803-224">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ae803-225">Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="ae803-225">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ae803-226">Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="ae803-226">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ae803-227">Обрабатывает результат любой операции выхода.</span><span class="sxs-lookup"><span data-stu-id="ae803-227">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ae803-228">Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="ae803-228">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ae803-229">Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="ae803-229">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ae803-230">Активирует операцию изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae803-230">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ae803-231">Активирует операцию регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae803-231">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ae803-232">Маршруты, представленные в предыдущей таблице, можно настраивать с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ae803-232">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ae803-233">При настройке параметров для предоставления пользовательских маршрутов убедитесь в том, что у приложения есть маршрут для обработки каждого пути.</span><span class="sxs-lookup"><span data-stu-id="ae803-233">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ae803-234">В приведенном ниже примере все пути имеют префикс `/security`.</span><span class="sxs-lookup"><span data-stu-id="ae803-234">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ae803-235">Компонент `Authentication` (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="ae803-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ae803-236">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae803-236">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="ae803-237">Если требуются совершенно разные пути, задайте маршруты, как описано выше, и обработайте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра действия:</span><span class="sxs-lookup"><span data-stu-id="ae803-237">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ae803-238">При необходимости можно разделить пользовательский интерфейс на несколько страниц.</span><span class="sxs-lookup"><span data-stu-id="ae803-238">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ae803-239">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ae803-239">Customize the authentication user interface</span></span>

<span data-ttu-id="ae803-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> содержит набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ae803-241">Каждое состояние можно настроить, передав пользовательский объект <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span><span class="sxs-lookup"><span data-stu-id="ae803-241">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="ae803-242">Чтобы настроить текст, отображаемый во время первоначального входа в систему, можно изменить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> указанным ниже образом.</span><span class="sxs-lookup"><span data-stu-id="ae803-242">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="ae803-243">Компонент `Authentication` (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="ae803-243">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ae803-244">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> есть фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в приведенной ниже таблице.</span><span class="sxs-lookup"><span data-stu-id="ae803-244">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ae803-245">Маршрут</span><span class="sxs-lookup"><span data-stu-id="ae803-245">Route</span></span>                            | <span data-ttu-id="ae803-246">Fragment</span><span class="sxs-lookup"><span data-stu-id="ae803-246">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="ae803-247">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="ae803-247">Customize the user</span></span>

<span data-ttu-id="ae803-248">Пользователей, привязанных к приложению, можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="ae803-248">Users bound to the app can be customized.</span></span> <span data-ttu-id="ae803-249">В приведенном ниже примере все пользователи, прошедшие проверку подлинности, получают утверждение `amr` для каждого из методов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-249">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ae803-250">Создайте класс, расширяющий класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>:</span><span class="sxs-lookup"><span data-stu-id="ae803-250">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ae803-251">Создайте фабрику, расширяющую <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span><span class="sxs-lookup"><span data-stu-id="ae803-251">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="ae803-252">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-252">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ae803-253">Допустима любая из следующих регистраций:</span><span class="sxs-lookup"><span data-stu-id="ae803-253">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="ae803-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="ae803-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="ae803-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>.</span><span class="sxs-lookup"><span data-stu-id="ae803-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ae803-257">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ae803-257">Support prerendering with authentication</span></span>

<span data-ttu-id="ae803-258">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="ae803-258">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ae803-259">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="ae803-259">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ae803-260">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="ae803-260">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ae803-261">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="ae803-261">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="ae803-262">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="ae803-262">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="ae803-263">В методе `Startup.Configure` серверного приложения замените [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) на [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="ae803-263">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ae803-264">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="ae803-264">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ae803-265">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ae803-265">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ae803-266">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ae803-266">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ae803-267">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="ae803-267">Update the file's contents:</span></span>

* <span data-ttu-id="ae803-268">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="ae803-268">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ae803-269">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="ae803-269">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ae803-270">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="ae803-270">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ae803-271">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae803-271">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ae803-272">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="ae803-272">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ae803-273">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ae803-273">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ae803-274">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="ae803-274">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ae803-275">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="ae803-275">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ae803-276">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="ae803-276">In this scenario:</span></span>

* <span data-ttu-id="ae803-277">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="ae803-277">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ae803-278">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="ae803-278">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ae803-279">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="ae803-279">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ae803-280">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="ae803-280">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ae803-281">Настройте Identity с помощью стороннего поставщика входа.</span><span class="sxs-lookup"><span data-stu-id="ae803-281">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ae803-282">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="ae803-282">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ae803-283">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ae803-283">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ae803-284">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="ae803-284">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ae803-285">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="ae803-285">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ae803-286">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="ae803-286">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ae803-287">Затем воспользуйтесь сторонним маркером доступа для вызова ресурсов стороннего API непосредственно из Identity в клиенте.</span><span class="sxs-lookup"><span data-stu-id="ae803-287">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ae803-288">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="ae803-288">We don't recommend this approach.</span></span> <span data-ttu-id="ae803-289">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="ae803-289">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ae803-290">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="ae803-290">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ae803-291">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="ae803-291">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ae803-292">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="ae803-292">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ae803-293">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="ae803-293">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ae803-294">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="ae803-294">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ae803-295">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="ae803-295">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ae803-296">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="ae803-296">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ae803-297">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="ae803-297">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ae803-298">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="ae803-298">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ae803-299">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="ae803-299">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="ae803-300">Использование конечных точек Open ID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="ae803-300">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="ae803-301">В библиотеке проверки подлинности и шаблонах Blazor используются конечные точки Open ID Connect (OIDC) версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="ae803-301">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="ae803-302">Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> маркера носителя JWT.</span><span class="sxs-lookup"><span data-stu-id="ae803-302">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="ae803-303">В следующем примере для AAD настраивается версия 2.0 путем добавления сегмента `v2.0` к свойству <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="ae803-303">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="ae803-304">Кроме того, параметр можно задать в файле параметров приложения (*appsettings.json*):</span><span class="sxs-lookup"><span data-stu-id="ae803-304">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="ae803-305">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую.</span><span class="sxs-lookup"><span data-stu-id="ae803-305">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="ae803-306">Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>, либо в файле параметров приложения (*appsettings.json*) с помощью ключа `Authority`.</span><span class="sxs-lookup"><span data-stu-id="ae803-306">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="ae803-307">Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="ae803-307">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="ae803-308">Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="ae803-308">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
