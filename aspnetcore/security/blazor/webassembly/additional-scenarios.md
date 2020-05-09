---
title: ASP.NET Core Blazor дополнительных сценариев безопасности для сборки
author: guardrex
description: Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e8a088b3f1a4e0eb7d5d1c5c09ef53c4a2bd3628
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976796"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="b5796-103">ASP.NET Core Блазор дополнительные сценарии безопасности для сборки</span><span class="sxs-lookup"><span data-stu-id="b5796-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="b5796-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="b5796-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="b5796-105">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="b5796-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="b5796-106">`AuthorizationMessageHandler` Службу можно использовать с `HttpClient` для присоединения маркеров доступа к исходящим запросам.</span><span class="sxs-lookup"><span data-stu-id="b5796-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="b5796-107">Токены получаются с помощью `IAccessTokenProvider` существующей службы.</span><span class="sxs-lookup"><span data-stu-id="b5796-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="b5796-108">Если токен не может быть получен, `AccessTokenNotAvailableException` создается исключение.</span><span class="sxs-lookup"><span data-stu-id="b5796-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="b5796-109">`AccessTokenNotAvailableException`содержит `Redirect` метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="b5796-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="b5796-110">Для `AuthorizationMessageHandler` настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с `ConfigureHandler` помощью метода.</span><span class="sxs-lookup"><span data-stu-id="b5796-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="b5796-111">В `AuthorizationMessageHandler` следующем примере `HttpClient` настраивается в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="b5796-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="b5796-112">Для удобства `BaseAddressAuthorizationMessageHandler` включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="b5796-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="b5796-113">Шаблоны Блазор, поддерживающие проверку подлинности, <xref:System.Net.Http.IHttpClientFactory> теперь используют в проекте API сервера для настройки <xref:System.Net.Http.HttpClient> с помощью: `BaseAddressAuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="b5796-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="b5796-114">При создании клиента с помощью `CreateClient` в предыдущем примере <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="b5796-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b5796-115">Настроенный <xref:System.Net.Http.HttpClient> затем используется для выполнения запросов с помощью простого `try-catch` шаблона.</span><span class="sxs-lookup"><span data-stu-id="b5796-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="b5796-116">Следующий `FetchData` компонент запрашивает данные прогноза погоды:</span><span class="sxs-lookup"><span data-stu-id="b5796-116">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="b5796-117">Кроме того, можно определить типизированный клиент, который обрабатывает все проблемы, связанные с получением маркеров HTTP и Token, в пределах одного класса:</span><span class="sxs-lookup"><span data-stu-id="b5796-117">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="b5796-118">*WeatherClient.CS*:</span><span class="sxs-lookup"><span data-stu-id="b5796-118">*WeatherClient.cs*:</span></span>

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="b5796-119">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5796-119">*Program.cs*:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="b5796-120">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5796-120">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a><span data-ttu-id="b5796-121">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="b5796-121">Request additional access tokens</span></span>

<span data-ttu-id="b5796-122">Маркеры доступа могут быть получены вручную путем вызова `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="b5796-122">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="b5796-123">В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты.</span><span class="sxs-lookup"><span data-stu-id="b5796-123">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="b5796-124">После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении (`Program.Main` *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="b5796-124">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="b5796-125">`IAccessTokenProvider.RequestToken` Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с заданным набором областей, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b5796-125">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
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

<span data-ttu-id="b5796-126">`TryGetToken`возврата</span><span class="sxs-lookup"><span data-stu-id="b5796-126">`TryGetToken` returns:</span></span>

* <span data-ttu-id="b5796-127">`true`с помощью `token` для использования.</span><span class="sxs-lookup"><span data-stu-id="b5796-127">`true` with the `token` for use.</span></span>
* <span data-ttu-id="b5796-128">`false`значение, если маркер не извлекается.</span><span class="sxs-lookup"><span data-stu-id="b5796-128">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b5796-129">HttpClient и HttpRequestMessage с параметрами запроса API FETCH</span><span class="sxs-lookup"><span data-stu-id="b5796-129">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="b5796-130">При выполнении в сборке Блазор в приложении [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage> может использоваться для настройки запросов.</span><span class="sxs-lookup"><span data-stu-id="b5796-130">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="b5796-131">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="b5796-131">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="b5796-132">Следующий пример выполняет `POST` запрос к КОНЕЧНОЙ точке API To Do List на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="b5796-132">The following example makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="b5796-133">Реализация `HttpClient` [виндоворворкерглобалскопе. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)в .NET-сборке использует.</span><span class="sxs-lookup"><span data-stu-id="b5796-133">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="b5796-134">Выборка позволяет настроить несколько [параметров для конкретного запроса](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b5796-134">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="b5796-135">Параметры запроса HTTP FETCH можно настроить с помощью `HttpRequestMessage` методов расширения, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="b5796-135">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="b5796-136">`HttpRequestMessage`метод расширения</span><span class="sxs-lookup"><span data-stu-id="b5796-136">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="b5796-137">Получение свойства запроса</span><span class="sxs-lookup"><span data-stu-id="b5796-137">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="b5796-138">информации</span><span class="sxs-lookup"><span data-stu-id="b5796-138">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="b5796-139">Мбайта</span><span class="sxs-lookup"><span data-stu-id="b5796-139">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="b5796-140">mode</span><span class="sxs-lookup"><span data-stu-id="b5796-140">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="b5796-141">контроля</span><span class="sxs-lookup"><span data-stu-id="b5796-141">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="b5796-142">Можно задать дополнительные параметры с помощью более универсального `SetBrowserRequestOption` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="b5796-142">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="b5796-143">HTTP-ответ обычно замещается в буфер приложения Блазор, чтобы обеспечить поддержку синхронных операций чтения содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="b5796-143">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="b5796-144">Чтобы включить поддержку потоковой передачи ответов, используйте `SetBrowserResponseStreamingEnabled` метод расширения для запроса.</span><span class="sxs-lookup"><span data-stu-id="b5796-144">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="b5796-145">Чтобы включить учетные данные в запрос между источниками, используйте `SetBrowserRequestCredentials` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="b5796-145">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="b5796-146">Дополнительные сведения о возможностях API-получения см. в разделе [MDN Web документация: виндоворворкерглобалскопе. fetch ():P араметерс](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b5796-146">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="b5796-147">При отправке учетных данных (файлов cookie или заголовков авторизации `Authorization` ) в запросах CORS этот заголовок должен быть РАЗРЕШЕН политикой CORS.</span><span class="sxs-lookup"><span data-stu-id="b5796-147">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="b5796-148">Следующая политика включает в себя настройку для:</span><span class="sxs-lookup"><span data-stu-id="b5796-148">The following policy includes configuration for:</span></span>

* <span data-ttu-id="b5796-149">Источники запроса (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="b5796-149">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="b5796-150">Любой метод (глагол).</span><span class="sxs-lookup"><span data-stu-id="b5796-150">Any method (verb).</span></span>
* <span data-ttu-id="b5796-151">`Content-Type`и `Authorization` заголовки.</span><span class="sxs-lookup"><span data-stu-id="b5796-151">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="b5796-152">Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>вызове метода.</span><span class="sxs-lookup"><span data-stu-id="b5796-152">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="b5796-153">Учетные данные, которые`credentials` `include`задаются кодом JavaScript на стороне клиента (свойство имеет значение).</span><span class="sxs-lookup"><span data-stu-id="b5796-153">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="b5796-154">Дополнительные сведения см. в <xref:security/cors> разделе и компоненте тестера HTTP-запросов примера приложения (*Components/хттпрекуесттестер. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b5796-154">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="b5796-155">Обработку ошибок запросов маркера</span><span class="sxs-lookup"><span data-stu-id="b5796-155">Handle token request errors</span></span>

<span data-ttu-id="b5796-156">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в поставщике удостоверений (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="b5796-156">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="b5796-157">Маркеры, которые IP-адрес выдает пользователю, обычно действительны в течение короткого периода времени, примерно один час, поэтому клиентское приложение должно регулярно получать новые токены.</span><span class="sxs-lookup"><span data-stu-id="b5796-157">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="b5796-158">В противном случае пользователь будет зарегистрирован после истечения срока действия назначенных токенов.</span><span class="sxs-lookup"><span data-stu-id="b5796-158">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="b5796-159">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры, не требуя от пользователя повторной проверки подлинности благодаря состоянию аутентификации или сеансу, который хранится в IP-адресе.</span><span class="sxs-lookup"><span data-stu-id="b5796-159">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="b5796-160">В некоторых случаях клиент не может получить маркер без взаимодействия с пользователем, например, когда по какой-либо причине пользователь явно выходит из IP.</span><span class="sxs-lookup"><span data-stu-id="b5796-160">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="b5796-161">Эта ситуация возникает при посещении `https://login.microsoftonline.com` и выходе пользователя из систему. В этих сценариях приложение не знает о немедленном выходе пользователя из систему. Любой токен, который удерживает клиент, может быть недействительным.</span><span class="sxs-lookup"><span data-stu-id="b5796-161">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="b5796-162">Кроме того, клиент не может подготавливать новый маркер без взаимодействия с пользователем после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="b5796-162">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="b5796-163">Эти сценарии не относятся к проверке подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="b5796-163">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="b5796-164">Они являются частью природы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="b5796-164">They are part of the nature of SPAs.</span></span> <span data-ttu-id="b5796-165">Проверка подлинности с помощью файлов cookie также не вызывает API сервера, если удаляется файл cookie для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="b5796-165">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="b5796-166">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="b5796-166">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="b5796-167">Чтобы создать новый маркер доступа для вызова API, пользователю может потребоваться выполнить повторную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-167">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="b5796-168">Даже если у клиента есть токен, который кажется допустимым, вызов сервера может завершиться ошибкой, так как маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="b5796-168">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="b5796-169">Когда приложение запрашивает маркер, существует два возможных результата:</span><span class="sxs-lookup"><span data-stu-id="b5796-169">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="b5796-170">Запрос выполнен успешно, и приложение имеет действительный токен.</span><span class="sxs-lookup"><span data-stu-id="b5796-170">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="b5796-171">Запрос завершается ошибкой, и приложение должно повторно пройти проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="b5796-171">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="b5796-172">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b5796-172">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="b5796-173">Существует несколько подходов, повышающих уровень сложности:</span><span class="sxs-lookup"><span data-stu-id="b5796-173">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="b5796-174">Сохранение текущего состояния страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="b5796-174">Store the current page state in session storage.</span></span> <span data-ttu-id="b5796-175">В `OnInitializeAsync`течение этого периода проверьте, можно ли восстановить состояние перед продолжением.</span><span class="sxs-lookup"><span data-stu-id="b5796-175">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="b5796-176">Добавьте параметр строки запроса и используйте его в качестве способа сигнализации приложению о необходимости повторного расконсервации ранее сохраненного состояния.</span><span class="sxs-lookup"><span data-stu-id="b5796-176">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="b5796-177">Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеанса без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="b5796-177">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="b5796-178">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="b5796-178">The following example shows how to:</span></span>

* <span data-ttu-id="b5796-179">Сохраните состояние перед перенаправлением на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="b5796-179">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="b5796-180">Восстановите предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="b5796-180">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="b5796-181">Сохранить состояние приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b5796-181">Save app state before an authentication operation</span></span>

<span data-ttu-id="b5796-182">Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес.</span><span class="sxs-lookup"><span data-stu-id="b5796-182">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="b5796-183">Это может быть так, если вы используете нечто вроде контейнера состояния и хотите восстановить состояние после завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-183">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="b5796-184">Можно использовать пользовательский объект состояния проверки подлинности, чтобы сохранить состояние конкретного приложения или ссылку на него, а затем восстановить это состояние после успешного завершения операции проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-184">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="b5796-185">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b5796-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="b5796-186">Настройка маршрутов приложений</span><span class="sxs-lookup"><span data-stu-id="b5796-186">Customize app routes</span></span>

<span data-ttu-id="b5796-187">По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` библиотека использует маршруты, показанные в следующей таблице, для представления различных состояний проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-187">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="b5796-188">Маршрут</span><span class="sxs-lookup"><span data-stu-id="b5796-188">Route</span></span>                            | <span data-ttu-id="b5796-189">Назначение</span><span class="sxs-lookup"><span data-stu-id="b5796-189">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="b5796-190">Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="b5796-190">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="b5796-191">Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="b5796-191">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="b5796-192">Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="b5796-192">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="b5796-193">Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="b5796-193">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="b5796-194">Обрабатывает результат операции выхода.</span><span class="sxs-lookup"><span data-stu-id="b5796-194">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="b5796-195">Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="b5796-195">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="b5796-196">Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="b5796-196">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="b5796-197">Активирует операцию для изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5796-197">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="b5796-198">Активирует операцию для регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5796-198">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="b5796-199">Маршруты, показанные в предыдущей таблице, можно настроить с `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`помощью.</span><span class="sxs-lookup"><span data-stu-id="b5796-199">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="b5796-200">При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.</span><span class="sxs-lookup"><span data-stu-id="b5796-200">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="b5796-201">В следующем примере все пути имеют префикс `/security`.</span><span class="sxs-lookup"><span data-stu-id="b5796-201">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="b5796-202">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b5796-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="b5796-203">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="b5796-203">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b5796-204">Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните `RemoteAuthenticatorView` визуализацию с помощью явного параметра Action:</span><span class="sxs-lookup"><span data-stu-id="b5796-204">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="b5796-205">При необходимости вы можете разбить пользовательский интерфейс на разные страницы.</span><span class="sxs-lookup"><span data-stu-id="b5796-205">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="b5796-206">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b5796-206">Customize the authentication user interface</span></span>

<span data-ttu-id="b5796-207">`RemoteAuthenticatorView`включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-207">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="b5796-208">Каждое состояние можно настроить путем передачи пользовательского `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="b5796-208">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="b5796-209">Для настройки отображаемого текста во время первоначального входа в систему может измениться следующим `RemoteAuthenticatorView` образом.</span><span class="sxs-lookup"><span data-stu-id="b5796-209">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="b5796-210">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b5796-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="b5796-211">В `RemoteAuthenticatorView` имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="b5796-211">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="b5796-212">Маршрут</span><span class="sxs-lookup"><span data-stu-id="b5796-212">Route</span></span>                            | <span data-ttu-id="b5796-213">Fragment (Фрагмент)</span><span class="sxs-lookup"><span data-stu-id="b5796-213">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="b5796-214">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="b5796-214">Customize the user</span></span>

<span data-ttu-id="b5796-215">Пользователи, привязанные к приложению, могут быть настроены.</span><span class="sxs-lookup"><span data-stu-id="b5796-215">Users bound to the app can be customized.</span></span> <span data-ttu-id="b5796-216">В следующем примере все пользователи, прошедшие проверку подлинности, получают `amr` утверждение для каждого из методов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5796-216">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="b5796-217">Создайте класс, расширяющий `RemoteUserAccount` класс:</span><span class="sxs-lookup"><span data-stu-id="b5796-217">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="b5796-218">Создайте фабрику, которая `AccountClaimsPrincipalFactory<TAccount>`расширяет:</span><span class="sxs-lookup"><span data-stu-id="b5796-218">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="b5796-219">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-219">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="b5796-220">Любая из следующих регистраций допустима:</span><span class="sxs-lookup"><span data-stu-id="b5796-220">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="b5796-221">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="b5796-221">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="b5796-222">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="b5796-222">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="b5796-223">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="b5796-223">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="b5796-224">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b5796-224">Support prerendering with authentication</span></span>

<span data-ttu-id="b5796-225">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="b5796-225">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="b5796-226">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="b5796-226">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="b5796-227">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="b5796-227">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="b5796-228">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="b5796-228">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="b5796-229">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="b5796-229">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="b5796-230">В методе `Startup.Configure` серверного приложения замените `endpoints.MapFallbackToFile("index.html")` на `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="b5796-230">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="b5796-231">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="b5796-231">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="b5796-232">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b5796-232">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="b5796-233">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b5796-233">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="b5796-234">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="b5796-234">Update the file's contents:</span></span>

* <span data-ttu-id="b5796-235">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="b5796-235">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="b5796-236">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="b5796-236">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="b5796-237">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="b5796-237">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="b5796-238">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5796-238">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="b5796-239">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="b5796-239">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="b5796-240">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="b5796-240">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="b5796-241">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="b5796-241">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="b5796-242">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="b5796-242">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="b5796-243">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="b5796-243">In this scenario:</span></span>

* <span data-ttu-id="b5796-244">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="b5796-244">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="b5796-245">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="b5796-245">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="b5796-246">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="b5796-246">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="b5796-247">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="b5796-247">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="b5796-248">Настройка Identity со сторонним поставщиком входа.</span><span class="sxs-lookup"><span data-stu-id="b5796-248">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="b5796-249">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="b5796-249">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="b5796-250">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5796-250">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="b5796-251">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="b5796-251">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="b5796-252">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="b5796-252">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="b5796-253">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="b5796-253">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="b5796-254">В нем используйте сторонний маркер доступа, чтобы вызывать сторонние ресурсы API непосредственно из Identity клиента.</span><span class="sxs-lookup"><span data-stu-id="b5796-254">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="b5796-255">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="b5796-255">We don't recommend this approach.</span></span> <span data-ttu-id="b5796-256">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="b5796-256">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="b5796-257">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="b5796-257">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="b5796-258">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="b5796-258">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="b5796-259">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="b5796-259">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="b5796-260">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="b5796-260">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="b5796-261">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="b5796-261">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="b5796-262">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="b5796-262">Make an API call from the client to the server API.</span></span> <span data-ttu-id="b5796-263">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="b5796-263">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="b5796-264">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="b5796-264">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="b5796-265">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="b5796-265">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="b5796-266">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="b5796-266">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
